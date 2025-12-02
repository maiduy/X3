# NATS JetStream Character-Related Subjects Documentation

## Overview
This document comprehensively defines all NATS JetStream subscription subjects used for character-related events in the game system. The pub/sub architecture enables microservices to communicate asynchronously and reliably through character state changes, progression events, and inventory updates.

**Architecture Pattern**: Event-Driven Microservices with NATS JetStream
**Persistence**: JetStream provides durable streams with at-least-once delivery guarantees
**Subject Naming Convention**: `game.{domain}.{entity}.{action}[.{qualifier}]`

---

## Stream Configuration

### Stream: GAME_PROGRESSION
Stores all character progression and development events.

**Configuration**:
- **Stream Name**: `GAME_PROGRESSION`
- **Subjects**: `game.progression.>`
- **Storage**: File (Durable)
- **Retention**: Limits (configurable based on business requirements)
- **Replicas**: 3 (High Availability)
- **Max Age**: 30 days (configurable)

---

## Character Subject Taxonomy

### 1. Character Lifecycle Events

#### 1.1 Character Unlock/Acquisition
**Subject**: `game.character.unlock`

**Purpose**: Published when a player unlocks a new character through gacha, fragments, events, or tutorials.

**Publishers**:
- **Gacha Service**: When player pulls a character from banner
- **Fragment Service**: When player collects enough character fragments
- **Quest/Event Service**: When character is awarded as quest/event reward
- **Tutorial Service**: When starter character is granted

**Subscribers**:
- **Player Profile Service**: Updates player's character collection
- **Achievement Service**: Tracks character collection achievements
- **Analytics Service**: Records character acquisition metrics
- **Notification Service**: Sends congratulatory notification to player

**Message Format**:
```json
{
  "event_id": "evt_uuid_12345",
  "timestamp": "2025-11-29T10:00:00Z",
  "user_id": "user_123",
  "character_id": "CHAR_FIRE_DRAGON",
  "unlock_method": "GACHA|FRAGMENT|EVENT|TUTORIAL|SHOP",
  "unlock_source": {
    "banner_id": "BANNER_NEW_YEAR",
    "event_id": "EVENT_WINTER_2025",
    "stage_id": "STAGE_1_BOSS"
  },
  "is_duplicate": false,
  "duplicate_conversion": {
    "item_id": "FRAGMENT_FIRE_DRAGON",
    "quantity": 10
  }
}
```

---

#### 1.2 Character Selection/Favoriting
**Subject**: `game.character.favorite`

**Purpose**: Published when player marks a character as favorite or changes default character.

**Publishers**:
- **Player Profile Service**: User preference updates

**Subscribers**:
- **Analytics Service**: Tracks character popularity
- **Recommendation Service**: Updates character recommendation algorithms

**Message Format**:
```json
{
  "event_id": "evt_uuid_67890",
  "timestamp": "2025-11-29T10:05:00Z",
  "user_id": "user_123",
  "character_id": "CHAR_FIRE_DRAGON",
  "action": "SET_FAVORITE|UNSET_FAVORITE|SET_DEFAULT",
  "previous_favorite": "CHAR_ICE_MAGE"
}
```

---

### 2. Character Progression Events

#### 2.1 Character Level Up
**Subject**: `game.progression.character.levelup`

**Purpose**: Core progression event when character gains enough experience to level up. Triggers stat recalculation and skill unlocks.

**Publishers**:
- **Character Service**: Monitors EXP accumulation and triggers level up
- **Battle Service**: Awards EXP after battle completion
- **Item Service**: When player uses EXP boosters

**Subscribers**:
- **Player Stat Calculator Service**: Recalculates stats using `GrowthCurves.json` formulas
- **Skill Service**: Checks for skill unlocks at new level
- **Mission/Quest Service**: Updates "Level up character" quest progress
- **LiveOps Service**: Tracks Battle Pass/event progression
- **Economy Service**: Deducts gold/resources if manual level up
- **Achievement Service**: Tracks level milestones
- **Analytics Service**: Records progression metrics
- **Notification Service**: Notifies player of level up rewards

**Message Format**:
```json
{
  "event_id": "evt_uuid_8888",
  "timestamp": "2025-11-29T10:00:00Z",
  "user_id": "user_123",
  "character_id": "CHAR_FIRE_DRAGON",
  "growth_curve_id": "CURVE_WARRIOR_S_TIER",
  "old_level": 19,
  "new_level": 20,
  "exp_source": "BATTLE|ITEM_USE|QUEST_REWARD",
  "exp_gained": 500,
  "total_exp": 15000,
  "reason": "STAGE_CLEAR|ITEM_USE|MANUAL_UPGRADE",
  "meta": {
    "stage_id": "STAGE_1_BOSS",
    "battle_id": "battle_uuid_456",
    "item_used": "ITEM_EXP_POTION_LARGE"
  }
}
```

**Follow-up Events Published**:
After processing, downstream services publish:
- `game.player.stats.updated` (from Stat Calculator)
- `game.character.skill.unlocked` (from Skill Service)

---

#### 2.2 Character Stats Updated
**Subject**: `game.player.stats.updated`

**Purpose**: Published after stat recalculation completes (triggered by level up, equipment change, or buff application).

**Publishers**:
- **Player Stat Calculator Service**: After computing new stats from growth curves

**Subscribers**:
- **Battle Service**: Updates character combat effectiveness
- **Player Profile Service**: Persists new stats to database
- **Leaderboard Service**: Updates combat power rankings
- **Client Notification Service**: Pushes updated stats to UI

**Message Format**:
```json
{
  "event_id": "evt_uuid_9999",
  "timestamp": "2025-11-29T10:00:15Z",
  "user_id": "user_123",
  "character_id": "CHAR_FIRE_DRAGON",
  "level": 20,
  "stats": {
    "hp": 2142,
    "atk": 257,
    "def": 151,
    "spd": 95,
    "crit": 0.15,
    "critDmg": 1.65,
    "accuracy": 0.95,
    "evasion": 0.08,
    "effectRes": 0.20
  },
  "combat_power": 5400,
  "stat_changes": {
    "hp_diff": +200,
    "atk_diff": +15,
    "def_diff": +10
  },
  "trigger_source": "LEVEL_UP|EQUIPMENT_CHANGE|BUFF_APPLIED|ASCENSION"
}
```

---

#### 2.3 Character Ascension
**Subject**: `game.progression.character.ascension`

**Purpose**: Published when character breaks through ascension tier, increasing level cap and base stats.

**Publishers**:
- **Ascension Service**: Validates materials and processes ascension

**Subscribers**:
- **Player Stat Calculator Service**: Recalculates stats with new ascension bonuses
- **Inventory Service**: Deducts ascension materials
- **Achievement Service**: Tracks ascension milestones
- **Analytics Service**: Records ascension metrics
- **Notification Service**: Celebrates ascension with player

**Message Format**:
```json
{
  "event_id": "evt_uuid_3333",
  "timestamp": "2025-11-29T11:00:00Z",
  "user_id": "user_123",
  "character_id": "CHAR_FIRE_DRAGON",
  "old_ascension": 2,
  "new_ascension": 3,
  "new_level_cap": 60,
  "materials_consumed": [
    {
      "item_id": "MAT_DRAGON_SCALE",
      "quantity": 15
    },
    {
      "item_id": "MAT_FIRE_ESSENCE",
      "quantity": 8
    }
  ],
  "gold_cost": 50000,
  "stat_bonuses": {
    "hp_bonus_pct": 0.05,
    "atk_bonus_pct": 0.05
  }
}
```

---

### 3. Character Skill Events

#### 3.1 Skill Unlock
**Subject**: `game.character.skill.unlocked`

**Purpose**: Published when character reaches required level to unlock a skill slot.

**Publishers**:
- **Skill Service**: Monitors level thresholds and unlocks skills

**Subscribers**:
- **Player Profile Service**: Updates available skills
- **Tutorial Service**: Triggers skill tutorial if first skill unlock
- **Analytics Service**: Tracks skill unlock patterns
- **Notification Service**: Alerts player of new skill

**Message Format**:
```json
{
  "event_id": "evt_uuid_4444",
  "timestamp": "2025-11-29T10:00:20Z",
  "user_id": "user_123",
  "character_id": "CHAR_FIRE_DRAGON",
  "skill_id": "SKILL_DRAGON_RAGE",
  "skill_slot": "skill2",
  "unlock_level": 20,
  "is_first_skill": false
}
```

---

#### 3.2 Skill Upgrade
**Subject**: `game.character.skill.upgraded`

**Purpose**: Published when player invests resources to upgrade a skill's level/potency.

**Publishers**:
- **Skill Service**: Processes skill level up requests

**Subscribers**:
- **Inventory Service**: Deducts upgrade materials
- **Economy Service**: Deducts gold cost
- **Achievement Service**: Tracks skill mastery achievements
- **Analytics Service**: Records skill investment patterns

**Message Format**:
```json
{
  "event_id": "evt_uuid_5555",
  "timestamp": "2025-11-29T12:00:00Z",
  "user_id": "user_123",
  "character_id": "CHAR_FIRE_DRAGON",
  "skill_id": "SKILL_DRAGON_RAGE",
  "old_skill_level": 5,
  "new_skill_level": 6,
  "materials_consumed": [
    {
      "item_id": "MAT_SKILL_BOOK_FIRE",
      "quantity": 3
    }
  ],
  "gold_cost": 10000
}
```

---

### 4. Character Equipment Events

#### 4.1 Equipment Equipped
**Subject**: `game.character.equipment.equipped`

**Purpose**: Published when player equips gear to a character slot.

**Publishers**:
- **Inventory Service**: Manages equipment assignment

**Subscribers**:
- **Player Stat Calculator Service**: Recalculates stats with equipment bonuses
- **Set Bonus Service**: Checks for gear set completion
- **Analytics Service**: Tracks equipment usage patterns

**Message Format**:
```json
{
  "event_id": "evt_uuid_6666",
  "timestamp": "2025-11-29T13:00:00Z",
  "user_id": "user_123",
  "character_id": "CHAR_FIRE_DRAGON",
  "equipment_slot": "WEAPON|HELMET|ARMOR|BOOTS|ACCESSORY_1|ACCESSORY_2",
  "equipped_item_id": "GEAR_SWORD_LEGENDARY_01",
  "equipped_item_instance_id": "item_inst_789",
  "previous_item_id": "GEAR_SWORD_RARE_05",
  "set_bonus_id": "SET_LIFESTEAL",
  "set_pieces_equipped": 2
}
```

---

#### 4.2 Equipment Unequipped
**Subject**: `game.character.equipment.unequipped`

**Purpose**: Published when player removes gear from a character.

**Publishers**:
- **Inventory Service**: Manages equipment removal

**Subscribers**:
- **Player Stat Calculator Service**: Recalculates stats without equipment
- **Set Bonus Service**: Removes set bonus if broken

**Message Format**:
```json
{
  "event_id": "evt_uuid_7777",
  "timestamp": "2025-11-29T13:05:00Z",
  "user_id": "user_123",
  "character_id": "CHAR_FIRE_DRAGON",
  "equipment_slot": "WEAPON",
  "unequipped_item_id": "GEAR_SWORD_LEGENDARY_01",
  "unequipped_item_instance_id": "item_inst_789",
  "set_bonus_lost": "SET_LIFESTEAL"
}
```

---

### 5. Character Team/Party Events

#### 5.1 Team Formation
**Subject**: `game.team.formed`

**Purpose**: Published when player creates or updates their team composition.

**Publishers**:
- **Team/Party Service**: Manages team configurations

**Subscribers**:
- **Matchmaking Service**: Uses team composition for matchmaking
- **Analytics Service**: Tracks popular team compositions
- **Synergy Calculator Service**: Calculates team synergies

**Message Format**:
```json
{
  "event_id": "evt_uuid_1111",
  "timestamp": "2025-11-29T14:00:00Z",
  "user_id": "user_123",
  "team_slot": 1,
  "team_name": "Fire Squad",
  "characters": [
    {
      "character_id": "CHAR_FIRE_DRAGON",
      "position": 1,
      "role": "DPS"
    },
    {
      "character_id": "CHAR_HOLY_KNIGHT",
      "position": 2,
      "role": "TANK"
    },
    {
      "character_id": "CHAR_NATURE_HEALER",
      "position": 3,
      "role": "SUPPORT"
    }
  ],
  "total_combat_power": 16200,
  "elemental_coverage": ["FIRE", "LIGHT", "NATURE"],
  "synergy_bonuses": [
    {
      "bonus_type": "FACTION_BONUS",
      "bonus_value": 0.10,
      "description": "3 Knights: +10% DEF"
    }
  ]
}
```

---

### 6. Character Combat Events

#### 6.1 Character Used in Battle
**Subject**: `game.battle.character.deployed`

**Purpose**: Published when character enters a battle instance.

**Publishers**:
- **Battle Service**: Tracks character usage in battles

**Subscribers**:
- **Analytics Service**: Records character usage statistics
- **Achievement Service**: Tracks "battles fought with character" achievements
- **Character Affinity Service**: Updates character bond/affinity levels

**Message Format**:
```json
{
  "event_id": "evt_uuid_2222",
  "timestamp": "2025-11-29T15:00:00Z",
  "user_id": "user_123",
  "battle_id": "battle_uuid_456",
  "battle_type": "PVE|PVP|RAID|EVENT",
  "character_id": "CHAR_FIRE_DRAGON",
  "character_level": 20,
  "character_position": 1,
  "team_composition": ["CHAR_FIRE_DRAGON", "CHAR_HOLY_KNIGHT", "CHAR_NATURE_HEALER"]
}
```

---

#### 6.2 Character Battle Performance
**Subject**: `game.battle.character.performance`

**Purpose**: Published at battle end with individual character performance metrics.

**Publishers**:
- **Battle Service**: Calculates performance after battle concludes

**Subscribers**:
- **Analytics Service**: Aggregates character effectiveness data
- **Leaderboard Service**: May factor into character-specific leaderboards
- **Recommendation Service**: Uses data for character recommendations

**Message Format**:
```json
{
  "event_id": "evt_uuid_3344",
  "timestamp": "2025-11-29T15:10:00Z",
  "user_id": "user_123",
  "battle_id": "battle_uuid_456",
  "character_id": "CHAR_FIRE_DRAGON",
  "battle_result": "WIN|LOSS|DRAW",
  "performance_metrics": {
    "damage_dealt": 45000,
    "damage_taken": 12000,
    "healing_done": 0,
    "kills": 2,
    "deaths": 0,
    "assists": 1,
    "skill_uses": {
      "SKILL_FIRE_CLAW": 8,
      "SKILL_FLAME_BREATH": 3,
      "SKILL_DRAGON_RAGE": 2,
      "SKILL_PHOENIX_STRIKE": 1
    },
    "mvp": true
  },
  "exp_gained": 500,
  "rewards": {
    "gold": 1000,
    "items": [
      {
        "item_id": "MAT_DRAGON_SCALE",
        "quantity": 2
      }
    ]
  }
}
```

---

### 7. Character Economy Events

#### 7.1 Character Fragment Collected
**Subject**: `game.character.fragment.collected`

**Purpose**: Published when player receives character fragments (shards).

**Publishers**:
- **Gacha Service**: Duplicate character pulls convert to fragments
- **Quest/Event Service**: Fragment rewards
- **Shop Service**: Fragment purchases

**Subscribers**:
- **Inventory Service**: Adds fragments to player inventory
- **Character Service**: Checks if enough fragments to unlock character
- **Analytics Service**: Tracks fragment acquisition rates

**Message Format**:
```json
{
  "event_id": "evt_uuid_5566",
  "timestamp": "2025-11-29T16:00:00Z",
  "user_id": "user_123",
  "character_id": "CHAR_FIRE_DRAGON",
  "fragment_quantity": 10,
  "source": "DUPLICATE_GACHA|QUEST_REWARD|SHOP_PURCHASE|EVENT",
  "total_fragments": 45,
  "required_for_unlock": 80,
  "progress_pct": 0.5625
}
```

---

### 8. Character Configuration Events

#### 8.1 Character Config Updated
**Subject**: `game.config.character.updated`

**Purpose**: Published when Master Config Service updates character definitions (balance changes, new characters).

**Publishers**:
- **Master Config Service**: When admin publishes updated `Characters.json`

**Subscribers**:
- **All Game Services**: Invalidate local character data cache
- **Battle Service**: Reload character stats and abilities
- **Client Gateway**: Notify connected clients to refresh config
- **Analytics Service**: Log config version changes

**Message Format**:
```json
{
  "event_id": "evt_uuid_7788",
  "timestamp": "2025-11-29T17:00:00Z",
  "config_type": "CHARACTERS",
  "config_version": "1.3.5",
  "previous_version": "1.3.4",
  "changes": [
    {
      "character_id": "CHAR_FIRE_DRAGON",
      "change_type": "STAT_BALANCE|NEW_CHARACTER|SKILL_CHANGE",
      "description": "Increased base ATK from 120 to 125"
    }
  ],
  "affected_character_ids": ["CHAR_FIRE_DRAGON"],
  "rollback_available": true
}
```

---

## Wildcard Subject Patterns

### Character Events by User
**Pattern**: `game.*.character.*`
**Example Subscriber**: User Profile Service listening to all character events for a specific user
**Use Case**: Aggregate all character-related activities for user history

### Character Events by Type
**Pattern**: `game.progression.character.*`
**Example Subscriber**: Analytics Service tracking all progression events
**Use Case**: Generate progression funnels and analytics

### Specific Character Events
**Pattern**: `game.*.CHAR_FIRE_DRAGON.*`
**Example Subscriber**: Character-specific analytics or debugging
**Use Case**: Monitor all events for a specific character (debugging, balance analysis)

---

## Stream Subscriptions by Service

### Player Stat Calculator Service
**Subscribed Subjects**:
- `game.progression.character.levelup`
- `game.progression.character.ascension`
- `game.character.equipment.equipped`
- `game.character.equipment.unequipped`

**Queue Group**: `service-stats-calculator`
**Processing**: Synchronous with database persistence

---

### Skill Service
**Subscribed Subjects**:
- `game.progression.character.levelup`

**Queue Group**: `service-skills`
**Processing**: Checks level thresholds and publishes `game.character.skill.unlocked`

---

### Inventory Service
**Subscribed Subjects**:
- `game.progression.character.ascension`
- `game.character.skill.upgraded`
- `game.character.fragment.collected`
- `game.battle.character.performance`

**Queue Group**: `service-inventory`
**Processing**: Material deduction and reward distribution

---

### Achievement Service
**Subscribed Subjects**:
- `game.character.unlock`
- `game.progression.character.levelup`
- `game.progression.character.ascension`
- `game.battle.character.deployed`

**Queue Group**: `service-achievements`
**Processing**: Tracks progress toward character-related achievements

---

### Analytics Service
**Subscribed Subjects**:
- `game.*.*.*` (All events for comprehensive analytics)

**Queue Group**: `service-analytics`
**Processing**: Asynchronous logging and aggregation

---

### LiveOps Service
**Subscribed Subjects**:
- `game.progression.character.levelup`
- `game.character.unlock`
- `game.battle.character.performance`

**Queue Group**: `service-liveops`
**Processing**: Updates Battle Pass, event missions, and seasonal challenges

---

### Economy Service
**Subscribed Subjects**:
- `game.progression.character.ascension`
- `game.character.skill.upgraded`

**Queue Group**: `service-economy`
**Processing**: Gold/currency deduction validation and logging

---

### Leaderboard Service
**Subscribed Subjects**:
- `game.player.stats.updated`
- `game.battle.character.performance`

**Queue Group**: `service-leaderboard`
**Processing**: Updates combat power rankings and character-specific leaderboards

---

## Event Delivery Guarantees

### At-Least-Once Delivery
JetStream guarantees that subscribers will receive events at least once. Services must implement **idempotent processing** to handle potential duplicate deliveries.

**Idempotency Strategy**:
```json
{
  "event_id": "evt_uuid_8888",
  "idempotency_key": "user_123:CHAR_FIRE_DRAGON:levelup:19to20"
}
```

Services should check `idempotency_key` against a processed events cache (Redis) before processing.

---

## Error Handling and Dead Letter Queue

### Failed Event Processing
When a subscriber fails to process an event after max retries:

**Dead Letter Subject**: `game.dlq.{original_subject}`
**Example**: `game.dlq.progression.character.levelup`

**DLQ Consumer**: Operations monitoring service for manual intervention

---

## Performance Considerations

### Message Size Limits
- **Recommended Max**: 256 KB per message
- **Character Stats**: ~2 KB
- **Battle Performance**: ~5 KB
- **Config Updates**: Can be large; consider using CDN references

### Throughput Estimates
- **Peak CCU**: 100,000 users
- **Average Events/User/Hour**: 50 character-related events
- **Peak Events/Second**: ~1,400 events/sec
- **JetStream Capacity**: Easily handles 10,000+ events/sec per node

---

## Monitoring and Observability

### Key Metrics to Track
1. **Event Publish Rate** per subject
2. **Consumer Lag** (time between publish and consumption)
3. **Failed Deliveries** and retry counts
4. **Stream Storage Size** and growth rate
5. **Duplicate Processing Rate** (idempotency check hits)

### Alerting Thresholds
- Consumer lag > 5 seconds
- Failed delivery rate > 1%
- Stream storage > 80% capacity

---

## Security Considerations

### Event Authentication
All published events must include:
- **user_id**: Verified against JWT claims
- **timestamp**: Within 5-minute window to prevent replay attacks
- **signature**: HMAC signature of event payload (optional for internal services)

### Authorization
Services must validate:
- User owns the character being modified
- Action is permitted for user's current state (e.g., has required materials)

---

## Versioning Strategy

### Event Schema Evolution
Use **backward-compatible changes** only:
- ✅ Add optional fields
- ✅ Add new event types
- ❌ Remove fields (deprecate for 6+ months first)
- ❌ Change field types

### Version Field
All events include:
```json
{
  "schema_version": "1.0.0"
}
```

Consumers must gracefully handle unknown fields.

---

## Testing Strategies

### Integration Testing
1. Publish test events to `game.test.progression.character.levelup`
2. Verify all expected subscribers process event
3. Validate side effects (database updates, follow-up events)

### Load Testing
1. Simulate 10,000 concurrent level-ups
2. Measure consumer lag and processing time
3. Verify no event loss during high load

---

## References

### Related Configuration Files
- **Characters.json**: Character definitions (config/core/Characters.json)
- **GrowthCurves.json**: Stat growth formulas (config/core/GrowthCurves.json)
- **Skills.json**: Skill definitions (config/gameplay/Skills.json)
- **Items.json**: Material definitions (config/core/Items.json)

### Related Documentation
- **Pub-Sub.md**: General pub/sub architecture overview
- **Pub-Sub-03.md**: Character level-up event-driven design
- **KienTrucGame.md**: Full game server architecture
- **Model-Entity.md**: Character entity model details

---

## Changelog

### Version 1.0.0 (2025-11-29)
- Initial comprehensive documentation of all character-related NATS subjects
- Defined 15 core character event types
- Documented 8 subscriber service patterns
- Established naming conventions and message formats

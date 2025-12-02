# NATS Character Subjects - Quick Reference

## All Character-Related NATS JetStream Subjects

### Summary Table

| Subject | Category | Publishers | Primary Subscribers | Message Size | Frequency |
|---------|----------|------------|-------------------|--------------|-----------|
| `game.character.unlock` | Lifecycle | Gacha, Fragment, Quest, Tutorial | Profile, Achievement, Analytics | ~1 KB | Medium |
| `game.character.favorite` | Lifecycle | Profile Service | Analytics, Recommendation | ~500 B | Low |
| `game.progression.character.levelup` | Progression | Character, Battle, Item | Stat Calculator, Skill, Mission, LiveOps, Economy | ~2 KB | High |
| `game.player.stats.updated` | Progression | Stat Calculator | Battle, Profile, Leaderboard, Client | ~2 KB | High |
| `game.progression.character.ascension` | Progression | Ascension Service | Stat Calculator, Inventory, Achievement | ~1.5 KB | Low |
| `game.character.skill.unlocked` | Skills | Skill Service | Profile, Tutorial, Analytics | ~800 B | Medium |
| `game.character.skill.upgraded` | Skills | Skill Service | Inventory, Economy, Achievement | ~1 KB | Medium |
| `game.character.equipment.equipped` | Equipment | Inventory Service | Stat Calculator, Set Bonus, Analytics | ~1 KB | Medium |
| `game.character.equipment.unequipped` | Equipment | Inventory Service | Stat Calculator, Set Bonus | ~800 B | Medium |
| `game.team.formed` | Team/Party | Team/Party Service | Matchmaking, Analytics, Synergy Calculator | ~2 KB | Medium |
| `game.battle.character.deployed` | Combat | Battle Service | Analytics, Achievement, Affinity | ~1.5 KB | High |
| `game.battle.character.performance` | Combat | Battle Service | Analytics, Leaderboard, Recommendation | ~5 KB | High |
| `game.character.fragment.collected` | Economy | Gacha, Quest, Shop | Inventory, Character, Analytics | ~800 B | Medium |
| `game.config.character.updated` | Config | Master Config Service | All Services, Battle, Gateway | ~10 KB | Very Low |

---

## Subject Hierarchy Visualization

```
game
├── character
│   ├── unlock
│   ├── favorite
│   ├── skill
│   │   ├── unlocked
│   │   └── upgraded
│   ├── equipment
│   │   ├── equipped
│   │   └── unequipped
│   └── fragment
│       └── collected
├── progression
│   └── character
│       ├── levelup
│       └── ascension
├── player
│   └── stats
│       └── updated
├── team
│   └── formed
├── battle
│   └── character
│       ├── deployed
│       └── performance
└── config
    └── character
        └── updated
```

---

## Service Subscription Matrix

| Service | Subscribed Subjects | Queue Group | Processing Type |
|---------|-------------------|-------------|-----------------|
| **Player Stat Calculator** | `levelup`, `ascension`, `equipment.equipped`, `equipment.unequipped` | `service-stats-calculator` | Sync + DB Persist |
| **Skill Service** | `levelup` | `service-skills` | Sync |
| **Inventory Service** | `ascension`, `skill.upgraded`, `fragment.collected`, `battle.character.performance` | `service-inventory` | Sync + DB Persist |
| **Achievement Service** | `unlock`, `levelup`, `ascension`, `battle.character.deployed` | `service-achievements` | Async |
| **Analytics Service** | `game.*.*.*` (All events) | `service-analytics` | Async |
| **LiveOps Service** | `levelup`, `unlock`, `battle.character.performance` | `service-liveops` | Async |
| **Economy Service** | `ascension`, `skill.upgraded` | `service-economy` | Sync |
| **Leaderboard Service** | `stats.updated`, `battle.character.performance` | `service-leaderboard` | Async |

---

## Message Format Templates

### Standard Event Envelope
All events follow this base structure:
```json
{
  "event_id": "evt_uuid_{random}",
  "timestamp": "ISO 8601 UTC",
  "user_id": "user_{id}",
  "character_id": "CHAR_{NAME}_{ARCHETYPE}",
  "schema_version": "1.0.0",
  "... event-specific fields ..."
}
```

### Common Field Types
- **IDs**: String UUIDs or prefixed identifiers
- **Timestamps**: ISO 8601 format in UTC
- **Quantities**: Integer numbers
- **Percentages**: Float decimals (0.15 = 15%)
- **Enums**: SCREAMING_SNAKE_CASE strings

---

## Event Flow Examples

### Example 1: Character Level Up Flow
```
1. Battle Service awards 500 EXP
   └─> Character Service detects level threshold crossed
       └─> PUBLISHES: game.progression.character.levelup

2. Stat Calculator Service SUBSCRIBES
   └─> Calculates new stats using GrowthCurves.json
       └─> PUBLISHES: game.player.stats.updated

3. Skill Service SUBSCRIBES to levelup
   └─> Checks if level 20 unlocks skill2
       └─> PUBLISHES: game.character.skill.unlocked

4. Mission Service SUBSCRIBES to levelup
   └─> Updates quest progress "Level up Fire character to 20"
       └─> Awards quest rewards

5. Analytics Service SUBSCRIBES to all events
   └─> Logs progression metrics to data warehouse
```

### Example 2: Equipment Change Flow
```
1. Player equips Legendary Sword
   └─> Inventory Service validates ownership
       └─> PUBLISHES: game.character.equipment.equipped

2. Stat Calculator Service SUBSCRIBES
   └─> Recalculates stats with weapon bonuses
       └─> PUBLISHES: game.player.stats.updated

3. Set Bonus Service SUBSCRIBES
   └─> Checks if player now has 2/4 Lifesteal set pieces
       └─> Applies set bonus if threshold met

4. Client Gateway SUBSCRIBES to stats.updated
   └─> Pushes WebSocket update to player's UI
       └─> Character sheet refreshes with new stats
```

### Example 3: Ascension Flow
```
1. Player initiates ascension (2 -> 3)
   └─> Ascension Service validates materials in inventory
       └─> PUBLISHES: game.progression.character.ascension

2. Inventory Service SUBSCRIBES
   └─> Deducts 15 Dragon Scales + 8 Fire Essence + 50,000 Gold
       └─> Persists to MongoDB

3. Stat Calculator Service SUBSCRIBES
   └─> Recalculates with ascension tier 3 bonuses
       └─> Level cap increased from 50 -> 60
       └─> PUBLISHES: game.player.stats.updated

4. Achievement Service SUBSCRIBES
   └─> Checks "Ascend 10 characters to tier 3"
       └─> Awards achievement if milestone reached
```

---

## Wildcard Subscription Patterns

### Pattern: All Character Events for Analytics
```
Subject: game.*.character.*
```
**Use Case**: Comprehensive analytics tracking

### Pattern: All Progression Events
```
Subject: game.progression.>
```
**Use Case**: Progression analytics dashboard

### Pattern: Specific Character Debugging
```
Subject: game.*.CHAR_FIRE_DRAGON.*
```
**Use Case**: Debug all events for one character

### Pattern: All Config Updates
```
Subject: game.config.>
```
**Use Case**: Cache invalidation across services

---

## Performance Benchmarks

### Expected Throughput (100K CCU)
- **Level Up Events**: ~140/sec (peak)
- **Stats Updated**: ~140/sec (downstream)
- **Equipment Changes**: ~80/sec
- **Battle Deployed**: ~500/sec (peak during matchmaking)
- **Battle Performance**: ~500/sec (end of battles)
- **Fragment Collected**: ~50/sec
- **Skill Unlocked**: ~20/sec

**Total Character Events**: ~1,420 events/sec at peak

### JetStream Configuration
- **3-node cluster** with replication factor 3
- **File storage** with 30-day retention
- **Max message size**: 256 KB
- **Estimated storage**: ~2 GB/day for character events

---

## Idempotency Keys Format

To prevent duplicate processing, use these key formats:

| Event Type | Idempotency Key Format |
|------------|----------------------|
| Level Up | `{user_id}:{character_id}:levelup:{old_level}to{new_level}` |
| Ascension | `{user_id}:{character_id}:ascend:{old_tier}to{new_tier}` |
| Skill Unlock | `{user_id}:{character_id}:skill:{skill_id}:unlock` |
| Equipment | `{user_id}:{character_id}:equip:{slot}:{item_instance_id}:{timestamp}` |

**Storage**: Redis with 24-hour TTL

---

## Error Scenarios & Dead Letter Queue

### Common Failure Cases
1. **Database Timeout**: Retry 3x with exponential backoff
2. **Inventory Validation Failure**: Send to DLQ for manual review
3. **Config Not Found**: Alert operations, retry after config refresh

### DLQ Subjects
- `game.dlq.progression.character.levelup`
- `game.dlq.character.equipment.equipped`
- `game.dlq.progression.character.ascension`

**Monitoring**: Operations dashboard for DLQ consumers

---

## Security Checklist

### Event Publishing
- ✅ Verify user_id matches authenticated session
- ✅ Validate character ownership before publishing
- ✅ Rate limit per user (max 100 character events/min)
- ✅ Sanitize all string inputs

### Event Consumption
- ✅ Verify event timestamp within 5-minute window
- ✅ Validate character exists in master config
- ✅ Check idempotency key before processing
- ✅ Use database transactions for multi-step operations

---

## Migration & Rollback Plan

### Adding New Event Type
1. Define event schema in this document
2. Implement publisher with feature flag OFF
3. Deploy subscribers to staging
4. Enable feature flag in staging
5. Monitor for 48 hours
6. Production rollout with gradual % ramp

### Breaking Schema Change
1. **DO NOT** make breaking changes
2. If absolutely required:
   - Create new subject with version suffix (e.g., `game.character.unlock.v2`)
   - Run dual publishers for 6 months
   - Migrate subscribers gradually
   - Deprecate old subject after 100% migration

---

## Quick Command Reference

### Publish Test Event (using NATS CLI)
```bash
# Level up event
nats pub game.progression.character.levelup \
  '{"event_id":"test_123","timestamp":"2025-11-29T10:00:00Z","user_id":"user_test","character_id":"CHAR_FIRE_DRAGON","old_level":19,"new_level":20,"growth_curve_id":"CURVE_WARRIOR_S_TIER"}'

# Stats updated event
nats pub game.player.stats.updated \
  '{"event_id":"test_456","timestamp":"2025-11-29T10:00:15Z","user_id":"user_test","character_id":"CHAR_FIRE_DRAGON","level":20,"stats":{"hp":2142,"atk":257},"combat_power":5400}'
```

### Monitor Subject Activity
```bash
# Watch all character events
nats sub "game.*.character.*"

# Watch progression events only
nats sub "game.progression.>"

# Monitor consumer lag
nats consumer report GAME_PROGRESSION
```

### Create Stream (First-time Setup)
```bash
nats stream add GAME_PROGRESSION \
  --subjects "game.progression.>" \
  --storage file \
  --replicas 3 \
  --retention limits \
  --max-age 30d
```

---

## Related Documentation

- **NATS-Character-Subjects.md**: Comprehensive detailed documentation
- **Pub-Sub-03.md**: Character level-up event-driven design
- **Characters.schema.md**: Character entity schema
- **GrowthCurves.schema.md**: Stat growth formulas
- **KienTrucGame.md**: Full microservices architecture

---

## Support & Contact

For questions about character event subjects:
- **Architecture**: Review KienTrucGame.md
- **Message Formats**: See NATS-Character-Subjects.md
- **Testing**: Use NATS CLI commands above
- **Monitoring**: Check NATS monitoring dashboard

---

**Last Updated**: 2025-11-29
**Document Version**: 1.0.0
**Maintained By**: Game Services Architecture Team

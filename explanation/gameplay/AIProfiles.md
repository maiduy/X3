
---

# 🤖 **AI Profiles Overview**

File JSON này định nghĩa các **AI profile** cho nhân vật hoặc quái vật trong game. Mỗi profile xác định:

* **Meta info**: tên, mô tả, tag
* **Behavior Tree**: logic hành vi (sequence, selector, condition, action, phase)
* **Target Priority**: trọng số ưu tiên mục tiêu
* **Skill Usage Rules**: khi nào và cách sử dụng skill / ultimate / buff / heal / debuff

Mỗi AI profile có thể được **gán cho các nhân vật NPC, Boss, hoặc Units**, giúp chúng phản ứng linh hoạt theo chiến trường.

---

# 🧩 **1. Profile Structure**

| Thuộc tính      | Ý nghĩa                                                                   |
| --------------- | ------------------------------------------------------------------------- |
| id              | Unique AI profile ID                                                      |
| meta            | Metadata: name, description, tags                                         |
| behaviorTree    | Cây hành vi: root node, sequence, selector, condition, action…            |
| targetPriority  | Trọng số mục tiêu, role modifiers, adaptive weighting…                    |
| skillUsageRules | Quy tắc sử dụng skill: khi nào, target selection, min/max, cooldown, AoE… |

---

# 🔹 **2. Các Profile Tiêu Biểu**

### **2.1 AI_AGGRESSIVE_DPS**

* Focus: Tấn công tối đa các mục tiêu ưu tiên
* Behavior Tree:

  * Dùng **Ultimate** nếu sẵn sàng → target: LowestHPEnemy
  * Dùng **Skill** nếu có skill mạnh → target: HighestThreatEnemy
  * Fallback: BasicAttack → LowestHPEnemy
* Target Priority: ưu tiên lowHP > highThreat > support > squishy > tank
* Skill Rules: ultimate khi full energy, skill ưu tiên AoE, basic attack fallback

---

### **2.2 AI_DEFENSIVE_SUPPORT**

* Focus: Hỗ trợ, chữa máu, bảo vệ đồng minh
* Behavior Tree:

  * Heal ally HP < 30% → target: LowestHPAlly
  * Skill nếu ally HP < 60% → LowestHPAlly
  * Buff đồng minh → target: HighestDPSAlly
  * Nếu không → BasicAttack nearest enemy
* Target Priority: allyLowHP > allyDebuffed > allyNeedsBuff > selfPreservation
* Skill Rules: heal, buff, cleanse theo tình trạng đồng minh

---

### **2.3 AI_TACTICAL_CONTROLLER**

* Focus: CC & debuff kẻ thù
* Behavior Tree:

  * CC enemy charging ultimate → HighestEnergyEnemy
  * AoE CC nếu nhiều kẻ thù nhóm → EnemyCluster
  * Debuff → HighestThreatEnemy
  * BasicAttack fallback → HighestThreatEnemy
* Target Priority: highEnergy, notCCd, noCCResistance, highThreat, clustered
* Skill Rules: CC, debuff stacking, AoE nếu minTargets ≥ 3

---

### **2.4 AI_BALANCED_ALLROUNDER**

* Focus: Linh hoạt, thích ứng theo battlefield
* Behavior Tree:

  * Nếu team HP thấp → Defensive (AI_DEFENSIVE_SUPPORT)
  * Nếu enemy HP thấp → Aggressive (AI_AGGRESSIVE_DPS)
  * Else → Balanced action, BestTarget
* Target Priority: weights adaptive, situational, opportunistic, balanced
* Skill Rules: adaptive mỗi turn, switch strategy theo healthThreshold hoặc turnThreshold

---

### **2.5 AI_BOSS_PATTERN**

* Focus: Boss multi-phase với hành vi phức tạp
* Behavior Tree: PhaseSelector theo HP

  * Phase1 (66-100% HP): pattern cơ bản (basic attack & skill1)
  * Phase2 (33-66% HP): summon minions, AoESkill, buff self
  * Phase3 (0-33% HP): EnrageMode + Ultimate + skill pattern
* Target Priority: phaseDependent
* Skill Rules: phasePattern, enrageThreshold, các hành vi theo phase

---

# 🏗 **3. Tổng hợp**

* **Cấu trúc AI linh hoạt**: từ DPS, Support, Controller, All-Rounder, tới Boss multi-phase
* **Behavior Tree mạnh mẽ**: Selector, Sequence, Condition, Action, PhaseSelector
* **Target Priority** có thể điều chỉnh theo role hoặc HP / threat
* **Skill Usage Rules** định nghĩa khi nào skill được dùng, AoE, stacking, buffs, heals
* Có thể **chèn AI sub-behavior**, adaptive hoặc phase-based để tạo trải nghiệm chiến đấu phức tạp

---

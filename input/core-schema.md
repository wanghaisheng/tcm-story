好的，基于我们之前的讨论和您的优秀扩展，我现在将整合所有内容，力求给出一份尽可能“完整”的数据设计。这里的“完整”是指覆盖核心实体、关系、多题材支持、社交UGC以及一些关键的辅助表。我将继续以PostgreSQL为参考，并加入一些更细致的字段和注释。

**请注意：** 即使是“完整”的设计，在实际项目迭代中也可能需要根据具体需求进行调整和优化。这份设计旨在提供一个强大且灵活的起点。

---

## 完整数据设计文档

### 一、核心卡牌体系 (Core Card System)

#### 1.1 `cards` (主卡牌表)
存储所有卡牌的通用基础信息。

```sql
CREATE TABLE cards (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(), -- 主键，自动生成UUID
    internal_name TEXT UNIQUE NOT NULL, -- 内部唯一标识名，便于开发和配置 (如 "herb_licorice", "formula_bzyqt")
    name_i18n JSONB NOT NULL, -- 多语言名称 (如 {"en": "Licorice Root", "zh_CN": "甘草"})
    description_i18n JSONB, -- 多语言描述
    rarity TEXT NOT NULL CHECK (rarity IN ('common', 'uncommon', 'rare', 'epic', 'legendary', 'mythic')), -- 稀有度
    category TEXT NOT NULL CHECK (category IN ('herb', 'formula', 'syndrome', 'symptom', 'disease')), -- 卡牌类型
    color_tag TEXT, -- 主题颜色标签 (如 "yellow_earth", "blue_water", "dream_purple") - 用于UI或题材关联
    icon_path TEXT, -- 卡牌图标资源路径 (如 "icons/herbs/licorice.png")
    artwork_path TEXT, -- 卡牌完整立绘/艺术图资源路径
    story_i18n JSONB, -- 背景故事/典故 (多语言)
    effects_description_i18n JSONB, -- 功能效果的文本描述 (多语言，如 {"en": "Tonifies Qi", "zh_CN": "补气"})
    base_effect_tags TEXT[], -- 基础效果标签数组 (程序用，如 ["TONIFY_QI", "HARMONIZE"])
    theme_affinity JSONB, -- 题材亲和度/标签 (如 {"tcm": 5, "myth": 3, "foodie": 4, "music_element": "GONG"})
    unlock_requirements JSONB, -- 解锁条件 (如 {"player_level": 10, "completed_quest_id": "q005"})
    is_active BOOLEAN DEFAULT TRUE, -- 卡牌是否在当前版本激活可用
    version INTEGER DEFAULT 1, -- 数据版本，用于数据迁移和管理
    created_at TIMESTAMPTZ DEFAULT NOW(),
    updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- 索引
CREATE INDEX idx_cards_category ON cards (category);
CREATE INDEX idx_cards_rarity ON cards (rarity);
CREATE INDEX idx_cards_internal_name ON cards (internal_name);
CREATE INDEX idx_cards_theme_affinity_gin ON cards USING GIN (theme_affinity); -- GIN索引用于JSONB查询
```
**字段说明补充:**
- `internal_name`: 用于程序内部引用，不随多语言改变。
- `color_tag`: 更灵活的颜色/主题标签，不局限于五行。
- `icon_path`, `artwork_path`: 存储视觉资源路径。
- `effects_description_i18n`: 用户可见的效果描述。
- `base_effect_tags`: 程序用于判断效果和组合逻辑的标签。
- `theme_affinity`: 描述此卡牌在不同题材下的“契合度”或特定属性，便于跨题材复用和筛选。
- `unlock_requirements`: 卡牌的获取/解锁条件。
- `is_active`: 控制卡牌是否对玩家可见和可用。

#### 1.2 卡牌专属详情表
为每种卡牌类型创建独立的详情表，通过 `card_id` 与 `cards` 表一对一关联。

##### `card_herb_details` (草药卡专属详情)
```sql
CREATE TABLE card_herb_details (
    card_id UUID PRIMARY KEY REFERENCES cards(id) ON DELETE CASCADE,
    -- 中医基础属性 (TCM Core)
    tcm_meridian_entry TEXT[], -- 归经 (如 ["LUNG", "SPLEEN"])
    tcm_property_flavor JSONB, -- 四气五味 (如 {"temperature": "WARM", "taste": "SWEET"})
    tcm_chemical_components TEXT[], -- (可选) 主要化学成分
    tcm_origin_area TEXT, -- (可选) 道地产区

    -- 多题材扩展字段 (Multi-Theme Extensions)
    theme_taste_profile TEXT, -- 味觉炼金: 主要风味 (如 "sweet_earthy", "bitter_herbal")
    theme_myth_origin_story_key TEXT, -- 神话药宗: 神话起源故事的文本Key
    theme_dream_emotion_tags TEXT[], -- 梦境药师: 主要情绪标签 (如 ["calming", "nostalgic"])
    theme_chronoscape_era TEXT, -- 时间药旅: 所属历史时期/宇宙纪元 (如 "Tang_Dynasty", "Nova_Epoch_3")
    theme_beastgarden_growth_duration_hours INTEGER, -- 灵兽药园: 生长所需小时
    theme_pixelsquare_yield_per_day INTEGER, -- 像素药肆: 每日基础产量
    theme_soundscape_core_tone TEXT, -- 音药幻境: 核心音高/音色 (如 "C4_Flute", "Deep_Hum")

    -- 其他通用扩展
    alternative_names_i18n JSONB, -- 别名/俗名 (多语言)
    cultivation_difficulty INTEGER CHECK (cultivation_difficulty BETWEEN 1 AND 5) -- (可选) 培养/获取难度
);
```

##### `card_formula_details` (方剂卡专属详情)
```sql
CREATE TABLE card_formula_details (
    card_id UUID PRIMARY KEY REFERENCES cards(id) ON DELETE CASCADE,
    -- 中医基础属性 (TCM Core)
    tcm_usage_method_i18n JSONB, -- 用法用量 (多语言，如 {"decoction": "煎服，每日两次"})
    tcm_primary_actions_tags TEXT[], -- 主要功用标签 (如 ["TONIFY_QI_BLOOD", "DISPEL_WIND_COLD"])
    tcm_contraindications_i18n JSONB, -- 禁忌 (多语言)
    tcm_classic_source_text TEXT, -- (可选) 古籍出处

    -- 多题材扩展字段 (Multi-Theme Extensions)
    theme_taste_dish_type TEXT, -- 味觉炼金: 菜品类型 (如 "healing_soup", "energy_dessert")
    theme_myth_ritual_name_key TEXT, -- 神话药宗: 相关仪式/神祇的文本Key
    theme_dream_effect_on_dreamscape TEXT, -- 梦境药师: 对梦境环境的影响 (如 "stabilize_nightmare", "induce_lucidity")
    theme_chronoscape_invention_era TEXT, -- 时间药旅: 发明/流行时期
    theme_beastgarden_synergy_beast_type TEXT, -- 灵兽药园: 协同灵兽类型 (如 "fire_phoenix", "water_serpent")
    theme_pixelsquare_cure_success_rate DECIMAL(5,2), -- 像素药肆: 基础治愈成功率 (0-100.00)
    theme_soundscape_composition_type TEXT, -- 音药幻境: 曲式/乐章类型 (如 "soothing_lullaby", "battle_anthem")

    -- 其他通用扩展
    preparation_instructions_i18n JSONB, -- (泛用) 制作说明/合成步骤 (多语言)
    crafting_difficulty INTEGER CHECK (crafting_difficulty BETWEEN 1 AND 5) -- (可选) 制作难度
);
```

##### `card_syndrome_details` (症候卡专属详情)
```sql
CREATE TABLE card_syndrome_details (
    card_id UUID PRIMARY KEY REFERENCES cards(id) ON DELETE CASCADE,
    -- 中医基础属性 (TCM Core)
    tcm_pathogenesis_i18n JSONB, -- 病因病机 (多语言)
    tcm_differentiation_points_i18n JSONB, -- 辨证要点 (多语言)
    tcm_related_atomic_syndromes_ids UUID[], -- (可选) 关联的原子症候卡ID

    -- 多题材扩展字段 (Multi-Theme Extensions)
    theme_taste_imbalance_description_key TEXT, -- 味觉炼金: 味觉失衡表现的文本Key
    theme_myth_curse_origin_key TEXT, -- 神话药宗: 诅咒/邪祟来源的文本Key
    theme_dream_emotional_state_tags TEXT[], -- 梦境药师: 核心情绪状态 (如 ["anxiety_ridden", "hopelessness"])
    theme_chronoscape_historical_context_key TEXT, -- 时间药旅: 时代背景/流行原因的文本Key
    theme_beastgarden_affected_aura_type TEXT, -- 灵兽药园: 影响的灵气/光环类型
    theme_pixelsquare_debuff_effects JSONB, -- 像素药肆: 施加的负面效果 (如 {"stat_penalty": "strength_down", "duration": 30})
    theme_soundscape_dissonance_pattern TEXT -- 音药幻境: 不和谐音型/噪音特征
);
```

##### `card_symptom_details` (症状卡专属详情)
```sql
CREATE TABLE card_symptom_details (
    card_id UUID PRIMARY KEY REFERENCES cards(id) ON DELETE CASCADE,
    -- 中医基础属性 (TCM Core)
    tcm_nature TEXT CHECK (tcm_nature IN ('excess', 'deficiency', 'cold', 'heat', 'internal', 'external')), -- 症状性质 (虚实寒热表里)
    tcm_involved_organs_tags TEXT[], -- 涉及脏腑标签 (如 ["LIVER_QI_STAGNATION", "SPLEEN_DEFICIENCY"])

    -- 通用描述字段
    severity_level INTEGER CHECK (severity_level BETWEEN 1 AND 5), -- 严重程度 (1-5级)
    typical_location_i18n JSONB, -- 典型发生部位 (多语言)
    aggravating_factors_i18n JSONB, -- 加重因素 (多语言)
    alleviating_factors_i18n JSONB, -- 缓解因素 (多语言)

    -- 多题材扩展字段 (Multi-Theme Extensions)
    theme_taste_sensation_description_key TEXT, -- 味觉炼金: 异常味觉描述的文本Key
    theme_myth_omen_significance_key TEXT, -- 神话药宗: 预兆意义的文本Key
    theme_dream_manifestation_in_dream_key TEXT, -- 梦境药师: 在梦境中的具体表现的文本Key
    theme_chronoscape_era_specific_interpretation_key TEXT, -- 时间药旅:特定时代的解读文本Key
    theme_beastgarden_distress_signal_type TEXT, -- 灵兽药园: 灵兽发出的信号类型
    theme_pixelsquare_glitch_effect_description_key TEXT, -- 像素药肆: 故障/像素化表现的文本Key
    theme_soundscape_aberrant_sound_profile TEXT -- 音药幻境: 异常声音特征 (如 "high_pitch_screech", "broken_rhythm")
);
```

##### `card_disease_details` (疾病卡专属详情)
```sql
CREATE TABLE card_disease_details (
    card_id UUID PRIMARY KEY REFERENCES cards(id) ON DELETE CASCADE,
    -- 中医基础属性 (TCM Core)
    tcm_disease_category TEXT, -- (可选) 中医病名分类 (如 "外感病", "内伤病")
    tcm_prognosis_i18n JSONB, -- 预后 (多语言)

    -- 通用描述字段
    common_complications_i18n JSONB, -- 常见并发症 (多语言)
    diagnostic_criteria_summary_i18n JSONB, -- (可选) 诊断标准摘要 (多语言)
    epidemiology_notes_i18n JSONB, -- (可选) 流行病学注释 (多语言)

    -- 多题材扩展字段 (Multi-Theme Extensions)
    theme_taste_crisis_level INTEGER CHECK (theme_taste_crisis_level BETWEEN 1 AND 3), -- 味觉炼金: 味觉危机等级
    theme_myth_entity_name_key TEXT, -- 神话药宗: 关联的邪魔/神祇实体的文本Key
    theme_dream_core_trauma_key TEXT, -- 梦境药师: 核心创伤/梦魇源头的文本Key
    theme_chronoscape_historical_impact_key TEXT, -- 时间药旅: 对历史造成影响的文本Key
    theme_beastgarden_corruption_source TEXT, -- 灵兽药园: 腐化/疫病源头
    theme_pixelsquare_system_crash_code TEXT, -- 像素药肆: 系统崩溃代码/错误信息
    theme_soundscape_silence_threat_level INTEGER CHECK (theme_soundscape_silence_threat_level BETWEEN 1 AND 3) -- 音药幻境: 静默威胁等级
);
```

### 二、关系与组合体系 (Relationship & Combination System)

#### 2.1 `card_relationships` (通用卡牌关系表)
用于定义卡牌之间的通用语义关系，如“A克制B”，“A是B的前置条件”。

```sql
CREATE TABLE card_relationships (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    source_card_id UUID NOT NULL REFERENCES cards(id) ON DELETE CASCADE,
    target_card_id UUID NOT NULL REFERENCES cards(id) ON DELETE CASCADE,
    relationship_type TEXT NOT NULL, -- (如 "IS_ANTIDOTE_FOR", "IS_INGREDIENT_OF", "IS_SYMPTOM_OF", "IS_CURE_FOR", "IS_AGGRAVATED_BY")
    description_i18n JSONB, -- 关系描述
    strength_modifier DECIMAL, -- (可选) 关系强度/影响因子
    conditions JSONB, -- (可选) 此关系成立的条件 (如 {"theme": "myth", "player_has_item_id": "item001"})
    created_at TIMESTAMPTZ DEFAULT NOW(),
    UNIQUE (source_card_id, target_card_id, relationship_type) -- 避免重复关系
);
CREATE INDEX idx_card_relationships_source ON card_relationships (source_card_id);
CREATE INDEX idx_card_relationships_target ON card_relationships (target_card_id);
CREATE INDEX idx_card_relationships_type ON card_relationships (relationship_type);
```
**说明:** 这个表可以替代部分原有的多对多关系表，提供更灵活的语义关系定义。原有的如`card_formula_herbs`仍然可以保留，用于存储更具体的组合信息（如用量）。

#### 2.2 `card_formula_ingredients` (方剂成分表 - 原`card_formula_herbs`的增强版)
```sql
CREATE TABLE card_formula_ingredients (
    formula_card_id UUID NOT NULL REFERENCES cards(id) ON DELETE CASCADE, -- 必须是 category='formula' 的卡牌
    ingredient_card_id UUID NOT NULL REFERENCES cards(id) ON DELETE CASCADE, -- 通常是 category='herb' 的卡牌
    quantity DECIMAL NOT NULL, -- 用量
    unit TEXT NOT NULL, -- 单位 (如 "g", "piece", "drop", "mana_unit")
    role_in_formula TEXT CHECK (role_in_formula IN ('monarch', 'minister', 'assistant', 'courier', 'catalyst', 'main_flavor')), -- 成分在方剂中的角色（君臣佐使/题材对应）
    preparation_method_key TEXT, -- (可选) 加工方法/烹饪技巧的文本Key
    is_essential BOOLEAN DEFAULT TRUE, -- 是否为核心/必需成分
    theme_specific_notes JSONB, -- (可选) 题材特定注释 (如 {"taste": "adds_sweetness_lvl_3", "myth": "requires_moonlight_infusion"})
    PRIMARY KEY (formula_card_id, ingredient_card_id, role_in_formula) -- 允许同一种草药以不同角色出现
);
CREATE INDEX idx_card_formula_ingredients_formula ON card_formula_ingredients (formula_card_id);
CREATE INDEX idx_card_formula_ingredients_ingredient ON card_formula_ingredients (ingredient_card_id);
```

#### 2.3 `dynamic_combinations` (动态组合规则表 - 原`combinations`的增强版)
存储定义动态组合（协同/互斥）的规则。

```sql
CREATE TABLE dynamic_combinations (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name_i18n JSONB NOT NULL, -- 组合名称
    description_i18n JSONB, -- 组合描述
    combination_type TEXT NOT NULL CHECK (combination_type IN ('synergy', 'conflict', 'enhancement', 'transformation')), -- 组合类型
    trigger_conditions JSONB NOT NULL, -- 触发条件 (核心！如 {"input_cards_match": [{"category": "herb", "theme_affinity.tcm": {"$gte": 3}}, {"base_effect_tags": {"$contains": ["TONIFY_QI"]}}], "min_input_count": 2, "required_crafting_tool_id": "tool_alchemy_table"})
    resulting_effects JSONB NOT NULL, -- 组合产生的效果 (如 [{"action": "ADD_EFFECT", "effect_tag": "SUPER_TONIFY_QI", "strength_multiplier": 1.5}, {"action": "ADD_SIDE_EFFECT", "effect_tag": "MILD_DIZZINESS"}])
    unlock_requirements JSONB, -- (可选) 玩家解锁此组合规则的条件
    priority INTEGER DEFAULT 0, -- 规则应用优先级
    theme_applicability_tags TEXT[], -- (可选) 此组合规则适用的题材标签
    is_active BOOLEAN DEFAULT TRUE,
    version INTEGER DEFAULT 1,
    created_at TIMESTAMPTZ DEFAULT NOW(),
    updated_at TIMESTAMPTZ DEFAULT NOW()
);
CREATE INDEX idx_dynamic_combinations_type ON dynamic_combinations (combination_type);
CREATE INDEX idx_dynamic_combinations_trigger_gin ON dynamic_combinations USING GIN (trigger_conditions);
```
**说明:**
- `trigger_conditions`: 使用类似MongoDB查询语言的JSON结构来定义复杂的触发条件，例如需要特定类别、特定标签、特定数量的卡牌，或者在特定制作工具/环境下。
- `resulting_effects`: 定义组合成功后产生的具体效果，可以是增强现有效果、添加新效果、产生副作用等。
- 这个表是实现“方剂的创造性和组合乐趣”的核心，规则引擎会基于此表进行判断。

### 三、探索、进度与掉落 (Exploration, Progress & Drops)

#### 3.1 `game_locations` (游戏地点/场景表)
```sql
CREATE TABLE game_locations (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name_i18n JSONB NOT NULL,
    description_i18n JSONB,
    location_type TEXT NOT NULL, -- (如 "world_map_region", "dungeon_level", "city_district", "dream_layer")
    theme_tags TEXT[], -- 地点所属题材 (如 ["myth", "forest"], ["pixelsquare", "market"])
    unlock_requirements JSONB, -- 进入/解锁此地点的条件
    available_resources_ids UUID[], -- (可选) 此地点可能产出的草药/物品卡ID池
    environmental_effects JSONB, -- (可选) 此地点的环境效果 (如 {"soundscape_ambient": "forest_sounds", "pixelsquare_glitch_chance": 0.05})
    parent_location_id UUID REFERENCES game_locations(id) ON DELETE SET NULL, -- (可选) 父级地点，用于构建层级地图
    map_coordinates JSONB -- (可选) 在世界地图上的坐标或相对位置
);
CREATE INDEX idx_game_locations_type ON game_locations (location_type);
CREATE INDEX idx_game_locations_theme_tags_gin ON game_locations USING GIN (theme_tags);
```

#### 3.2 `task_definitions` (任务/关卡定义表)
```sql
CREATE TABLE task_definitions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name_i18n JSONB NOT NULL,
    description_i18n JSONB,
    task_type TEXT NOT NULL, -- (如 "main_quest", "side_quest", "daily_challenge", "exploration_node")
    location_id UUID REFERENCES game_locations(id), -- (可选) 任务发生的地点
    theme_tags TEXT[],
    objectives JSONB NOT NULL, -- 任务目标 (如 [{"type": "COLLECT_ITEM", "item_id": "herb_ginseng", "quantity": 5}, {"type": "DEFEAT_ENEMY", "enemy_id": "dream_nightmare_A"}])
    rewards JSONB NOT NULL, -- 任务奖励 (如 [{"type": "EXP", "amount": 100}, {"type": "CARD_DROP_POOL", "pool_id": "pool_forest_herbs"}])
    unlock_requirements JSONB,
    is_repeatable BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMPTZ DEFAULT NOW()
);
CREATE INDEX idx_task_definitions_type ON task_definitions (task_type);
```

#### 3.3 `card_drop_pools` (卡牌掉落池定义)
```sql
CREATE TABLE card_drop_pools (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name TEXT UNIQUE NOT NULL, -- 掉落池名称 (如 "Forest_Herbs_Common", "Mythic_Beast_Parts_Rare")
    description TEXT
);
```

#### 3.4 `card_drop_pool_entries` (掉落池条目 - 定义池中有哪些卡，及其概率)
```sql
CREATE TABLE card_drop_pool_entries (
    pool_id UUID NOT NULL REFERENCES card_drop_pools(id) ON DELETE CASCADE,
    card_id UUID NOT NULL REFERENCES cards(id) ON DELETE CASCADE,
    drop_weight INTEGER NOT NULL DEFAULT 1, -- 掉落权重 (用于计算相对概率)
    min_quantity INTEGER DEFAULT 1,
    max_quantity INTEGER DEFAULT 1,
    conditions JSONB, -- (可选) 此条目生效的额外条件 (如 {"player_level_gte": 10})
    PRIMARY KEY (pool_id, card_id)
);
CREATE INDEX idx_card_drop_pool_entries_pool ON card_drop_pool_entries (pool_id);
```

#### 3.5 `user_card_inventory` (用户卡牌库存)
```sql
CREATE TABLE user_card_inventory (
    user_id UUID NOT NULL, -- 关联用户表
    card_id UUID NOT NULL REFERENCES cards(id) ON DELETE RESTRICT, -- RESTRICT防止意外删除已获得的卡牌定义
    quantity INTEGER NOT NULL DEFAULT 1,
    obtained_at TIMESTAMPTZ DEFAULT NOW(),
    is_new BOOLEAN DEFAULT TRUE, -- 是否为新获得，用于UI提示
    custom_data JSONB, -- (可选) 用户对此卡牌的特定数据 (如灵兽药园中某株草药的生长进度)
    PRIMARY KEY (user_id, card_id)
);
CREATE INDEX idx_user_card_inventory_user ON user_card_inventory (user_id);
```

#### 3.6 `user_progress_tracking` (用户进度追踪 - 原`user_progress`增强版)
```sql
CREATE TABLE user_progress_tracking (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL,
    progress_type TEXT NOT NULL, -- (如 "CARD_UNLOCK", "CARD_MASTERY", "LOCATION_DISCOVERY", "TASK_COMPLETION", "MYSTERY_REVEALED", "THEME_PROGRESS")
    target_id TEXT NOT NULL, -- 对应类型的ID (如 card_id, location_id, task_id, mystery_id, theme_name)
    status TEXT, -- (如 "locked", "unlocked", "in_progress", "completed", "mastered")
    current_value INTEGER, -- (可选) 当前进度值 (如掌握度、完成百分比)
    max_value INTEGER, -- (可选) 目标值
    meta_data JSONB, -- (可选) 额外元数据 (如 {"theme_name": "taste", "score": 85})
    achieved_at TIMESTAMPTZ,
    updated_at TIMESTAMPTZ DEFAULT NOW(),
    UNIQUE (user_id, progress_type, target_id)
);
CREATE INDEX idx_user_progress_tracking_user_type_target ON user_progress_tracking (user_id, progress_type, target_id);
```

### 四、奥秘与知识体系 (Mystery & Knowledge System)

#### 4.1 `knowledge_entries` (知识条目/百科 - 原`mysteries`扩展)
```sql
CREATE TABLE knowledge_entries (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    entry_type TEXT NOT NULL CHECK (entry_type IN ('lore', 'tcm_concept', 'historical_fact', 'game_mechanic_tip', 'mystery_reveal')), -- 条目类型
    title_i18n JSONB NOT NULL,
    content_i18n JSONB NOT NULL, -- 详细内容 (可以是富文本或Markdown)
    unlock_conditions JSONB NOT NULL, -- 解锁此条目的条件 (如 {"requires_card_ids": ["herb_ginseng"], "requires_combination_ids": ["combo_A"], "user_progress_target_id": "mystery_qi_deficiency"})
    related_card_ids UUID[], -- (可选) 与此知识条目相关的卡牌ID
    theme_tags TEXT[], -- (可选) 相关题材
    icon_path TEXT,
    sort_order INTEGER DEFAULT 0,
    is_secret BOOLEAN DEFAULT FALSE, -- 是否为隐藏/秘密条目
    created_at TIMESTAMPTZ DEFAULT NOW(),
    updated_at TIMESTAMPTZ DEFAULT NOW()
);
CREATE INDEX idx_knowledge_entries_type ON knowledge_entries (entry_type);
CREATE INDEX idx_knowledge_entries_theme_tags_gin ON knowledge_entries USING GIN (theme_tags);
```

### 五、社交与UGC体系 (Social & UGC System)

#### 5.1 `users` (用户表 - 基础)
```sql
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    username TEXT UNIQUE NOT NULL,
    hashed_password TEXT NOT NULL, -- 注意密码安全存储
    email TEXT UNIQUE,
    display_name_i18n JSONB,
    avatar_url TEXT,
    bio_i18n JSONB,
    preferences JSONB, -- (如 {"theme_preference": "myth", "language": "zh_CN"})
    last_login_at TIMESTAMPTZ,
    created_at TIMESTAMPTZ DEFAULT NOW(),
    updated_at TIMESTAMPTZ DEFAULT NOW()
);
```

#### 5.2 `user_generated_content` (用户生成内容 - 原`user_creations`增强版)
```sql
CREATE TABLE user_generated_content (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    author_user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    ugc_type TEXT NOT NULL, -- (如 "CUSTOM_FORMULA_RECIPE", "BEAST_DESIGN", "APOTHECARY_LAYOUT", "MUSIC_COMPOSITION", "DREAM_JOURNAL_ENTRY", "PLAYER_GUIDE")
    title_i18n JSONB NOT NULL,
    description_i18n JSONB,
    content_data JSONB NOT NULL, -- UGC核心数据 (如 {"formula_ingredients": [...], "crafting_steps": "..."} 或 {"beast_parts": [...], "abilities": [...]})
    theme_tags TEXT[], -- 相关题材
    privacy_setting TEXT NOT NULL DEFAULT 'public' CHECK (privacy_setting IN ('public', 'friends_only', 'private')),
    share_code TEXT UNIQUE, -- 用于站外分享的短码
    version INTEGER DEFAULT 1,
    status TEXT DEFAULT 'published' CHECK (status IN ('draft', 'pending_review', 'published', 'rejected', 'archived')), -- UGC状态，用于审核
    moderation_notes TEXT, -- (可选) 审核备注
    likes_count INTEGER DEFAULT 0,
    views_count INTEGER DEFAULT 0,
    shares_count INTEGER DEFAULT 0,
    comments_count INTEGER DEFAULT 0,
    created_at TIMESTAMPTZ DEFAULT NOW(),
    updated_at TIMESTAMPTZ DEFAULT NOW()
);
CREATE INDEX idx_ugc_author ON user_generated_content (author_user_id);
CREATE INDEX idx_ugc_type ON user_generated_content (ugc_type);
CREATE INDEX idx_ugc_theme_tags_gin ON user_generated_content USING GIN (theme_tags);
CREATE INDEX idx_ugc_likes_count ON user_generated_content (likes_count DESC); -- 用于热门排序
```

#### 5.3 `ugc_interactions` (UGC社交互动 - 原`user_interactions`增强版)
```sql
CREATE TABLE ugc_interactions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE, -- 执行操作的用户
    target_ugc_id UUID NOT NULL REFERENCES user_generated_content(id) ON DELETE CASCADE, -- 目标UGC
    interaction_type TEXT NOT NULL CHECK (interaction_type IN ('like', 'comment', 'share_internal', 'report', 'bookmark')), -- 互动类型
    comment_text TEXT, -- (如果类型是 'comment')
    report_reason TEXT, -- (如果类型是 'report')
    created_at TIMESTAMPTZ DEFAULT NOW()
);
CREATE INDEX idx_ugc_interactions_user ON ugc_interactions (user_id);
CREATE INDEX idx_ugc_interactions_target ON ugc_interactions (target_ugc_id);
CREATE INDEX idx_ugc_interactions_type ON ugc_interactions (interaction_type);
```
**注意:** `shares_count` 在 `user_generated_content` 表中可以作为冗余字段，通过触发器或应用层逻辑在 `ugc_interactions` 发生 `share_internal` 时更新，或者记录站外分享的打点。

#### 5.4 `user_relationships` (用户间关系，如好友、关注)
```sql
CREATE TABLE user_relationships (
    follower_user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    followed_user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    relationship_status TEXT NOT NULL DEFAULT 'pending' CHECK (relationship_status IN ('pending', 'accepted', 'blocked')), -- 'pending' 用于好友请求
    created_at TIMESTAMPTZ DEFAULT NOW(),
    PRIMARY KEY (follower_user_id, followed_user_id)
);
CREATE INDEX idx_user_relationships_followed ON user_relationships (followed_user_id);
```

### 六、辅助与配置表 (Auxiliary & Configuration Tables)

#### 6.1 `localization_strings` (多语言文本表)
```sql
CREATE TABLE localization_strings (
    string_key TEXT PRIMARY KEY, -- (如 "herb_licorice_name", "welcome_message_title")
    translations JSONB NOT NULL -- (如 {"en_US": "Licorice Root", "zh_CN": "甘草", "ja_JP": "甘草"})
);
```
**说明:** `cards` 表等中的 `_i18n` 字段可以存储JSON，或者更规范地存储一个指向此表`string_key`的引用。如果文本量巨大且更新频繁，后者更优。为简化，当前设计中`_i18n`字段直接存JSON。

#### 6.2 `game_settings` (全局游戏配置)
```sql
CREATE TABLE game_settings (
    setting_key TEXT PRIMARY KEY,
    setting_value JSONB NOT NULL,
    description TEXT,
    updated_at TIMESTAMPTZ DEFAULT NOW()
);
-- 示例: ('ugc_daily_submission_limit', '{"limit": 5}', '用户每日UGC提交上限')
```

#### 6.3 `asset_references` (资源引用表)
```sql
CREATE TABLE asset_references (
    asset_key TEXT PRIMARY KEY, -- (如 "icons/herbs/licorice.png", "sfx/ui/button_click.wav")
    asset_type TEXT NOT NULL, -- (如 "image", "audio", "video", "3d_model")
    storage_path TEXT NOT NULL, -- 实际存储路径 (可以是本地路径或CDN URL)
    version TEXT, -- 资源版本号，用于缓存控制
    meta_data JSONB -- (可选) 额外元数据 (如图片尺寸，音频时长)
);
```
**说明:** `cards` 表中的 `icon_path`, `artwork_path` 可以直接存储路径，或者引用此表的 `asset_key`。后者更利于资源管理和版本控制。

---

## 关系与数据流总结

*   **核心卡牌数据**是所有玩法的基础，通过 `cards` 表和各类 `card_xxx_details` 表定义。
*   **动态组合逻辑**由 `dynamic_combinations` 表驱动，其 `trigger_conditions` 和 `resulting_effects` 是核心，由游戏内的规则引擎解析执行。
*   **游戏世界与进程**通过 `game_locations`, `task_definitions`, `card_drop_pools` 和 `user_progress_tracking` 构建。
*   **知识与故事**通过 `knowledge_entries` 和卡牌自身的 `story_i18n` 等字段呈现。
*   **社交与UGC**由 `users`, `user_generated_content`, `ugc_interactions`, `user_relationships` 等表支持，是游戏长期运营和病毒传播的关键。
*   **多语言、配置、资源管理**由辅助表支持，确保系统的灵活性和可维护性。

## 最终优化与考量

1.  **触发器 (Triggers):**
    *   使用触发器自动更新 `updated_at` 字段。
    *   使用触发器维护冗余计数（如 `user_generated_content` 中的 `likes_count`），但需注意性能影响，高并发下可能考虑异步更新。
2.  **数据校验与约束 (Constraints):**
    *   除了已有的 `CHECK` 约束，可以在应用层或数据库层面（如使用自定义函数）对 `JSONB` 字段的结构进行更严格的校验。
3.  **安全性 (Security):**
    *   用户密码必须哈希加盐存储。
    *   对UGC内容进行必要的过滤和审核。
    *   API接口进行权限控制。
4.  **数据库选型特定功能：**
    *   PostgreSQL的 `JSONB` 操作符和 GIN 索引对于处理和查询JSON数据非常强大。
    *   利用 `NOTIFY/LISTEN` 实现轻量级实时消息推送（如UGC更新通知）。
5.  **演进式设计：**
    *   这套设计提供了一个全面的框架，但在项目初期，可以根据MVP（最小可行产品）的需求，先实现核心部分，再逐步扩展和完善。例如，复杂的`dynamic_combinations`规则引擎可以先用简化的版本，UGC功能也可以分阶段上线。

这份完整的数据设计文档应该能为您的多题材卡牌系统提供一个坚实且富有弹性的基础。它尝试平衡当前的明确需求与未来的不确定性，强调了模块化和可扩展性，以便应对各种创意“换皮”和功能迭代。

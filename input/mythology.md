好的，我们来深入细化“神话体系与神力恩赐 (Mythology & Divine Boons)”这个题材，将您的核心数据（疾病、草药、方剂、症状）融入一个充满神祇、信仰与奇迹的世界。

**核心世界观：万神殿之界 (Pantheon's Reach) - 信仰交织的凡尘**

万神殿之界是一个由众多神祇共同庇佑（或争夺信仰）的世界。每个神祇掌管着自然、情感、技艺或宇宙法则的某个方面。凡人通过祈祷、献祭和遵循教义来获得神祇的关注和“神恩”。然而，当神祇发怒、力量失衡，或凡人信仰动摇、触犯禁忌时，“神罚”或“邪祟”便会降临，带来疾病与灾厄。

**一、草药卡 -> 神赐圣草 (Divine Herbs) / 信仰余晖 (Faith's Embers)**

在万神殿之界，“草药”是沾染了神祇力量的植物，或是虔诚信徒祈祷时散落的信仰能量凝结物。

*   **基础字段映射:**
    *   `name`: 圣草/余晖名称 (如：阿波罗的月桂叶、盖亚的生命花蜜、赫尔墨斯的迅捷草、战神祝福的龙血石)
    *   `description`: 蕴含的神力特质 (如：“太阳神阿波罗的祝福，能驱散阴影，带来光明与活力。” / “大地母神盖亚的精华，能滋养万物，修复受损的生机。”)
    *   `rarity`: 神圣程度/信仰纯度 (普通信徒的祈愿、英雄的遗物、神祇亲手播种的圣物)
    *   `category`: "神赐圣草" / "信仰余晖"
    *   `color/标签`: 所属神系/神祇领域 (如：奥林匹斯神系-光明、北欧神系-力量、埃及神系-守护、自然神祇-生命)
    *   `effects`: 基础神恩效果 (如：微弱的生命恢复、短暂的力量提升、轻微的诅咒抗性)
    *   `story`: 关于此圣草的起源神话，如某神祇为凡人降下的奇迹，或是某英雄受神启示发现的宝物。
    *   `chemical_components` -> **`神力印记 (Divine Sigil)` / `神性粒子浓度 (Theoplasmic Particle Density)`**: `jsonb` (如：`{"Zeus_Lightning": 75, "Hera_Order": 20}`，数值代表该神祇力量的强度或特性 / 或直接是该神祇的圣徽)
    *   `meridian_entry` (归经) -> **`神恩流注路径 (Divine Grace Channel)`**: `text[]` (如：“生命神殿（心脏）”、“智慧圣堂（大脑）”、“力量祭坛（肌肉）”、“守护结界（皮肤）” - 对应神力最易作用的身体部位或灵魂层面)
    *   `property_flavor` (四气五味) -> **`神力特质 (Theological Properties)`**: `jsonb`
        *   **`神性倾向 (Divine Inclination)`**: (对应四气)
            *   `炽热神恩 (Ardent Grace)`: 如太阳神、火神、战神之力，倾向于激励、净化（火焰）、赋予勇气。
            *   `凛冽神威 (Chilling Majesty)`: 如冥界神、冬之神、审判之神之力，倾向于镇静、惩戒、剥离（负面状态）。
            *   `温和神佑 (Gentle Blessing)`: 如丰收女神、治愈之神、家庭女神之力，倾向于滋养、抚慰、稳固。
            *   `清凉神启 (Cooling Revelation)`: 如月亮女神、智慧之神、泉水之神之力，倾向于启迪、安抚、净化（心灵）。
        *   **`信仰共鸣 (Faith Resonance)`**: (对应五味，更偏向信仰的感受或神力的作用方式)
            *   `神圣之锋 (Sacred Edge)`: 类似“辛”，穿透邪恶，直达核心，激发潜能。
            *   `苦口忠言 (Bitter Truth)`: 类似“苦”，揭示真相（即使残酷），净化心灵的虚妄。
            *   `甘甜赐福 (Sweet Boon)`: 类似“甘”，带来喜悦、满足与和谐，是纯粹的恩惠。
            *   `戒律之缚 (Binding Tenet)`: 类似“酸”，收束放纵，强化秩序，但也可能带来束缚感。
            *   `信仰基石 (Foundation of Faith)`: 类似“咸/淡”，稳固信仰，作为其他神恩的载体，调和神力。
    *   `origin`: 发现地/赐予者 (如：奥林匹斯圣山、世界树的根部、某神的废弃神庙、虔诚牧师的祭坛)

**二、方剂卡 -> 祈祷仪式 (Prayer Rituals) / 神酿圣水 (Divine Nectars) / 祝福护符 (Blessed Talismans)**

通过特定的祈祷、献祭或神启的配方，将多种神赐圣草的力量结合，向神祇祈求更强大的神恩或制作蕴含神力的物品。

*   **基础字段映射:**
    *   `category`: "祈祷仪式" / "神酿圣水" / "祝福护符"
    *   `herbs` (草药搭配) -> **`祭品/圣物组合 (Offerings/Sacred Components)`**: `array` (关联神赐圣草卡，并可注明所需的“仪式地点”如“神殿主祭坛”、“圣泉边”，或“媒介”如“圣火”、“吟唱的赞美诗”)
    *   `usage`: 启动方式/使用方法 (如：“在特定时辰向特定神祇虔诚祈祷”、“饮用圣水”、“佩戴护符”、“吟诵祷文激活”)
    *   `effect` (治疗效果) -> **`核心神迹效应 (Primary Miraculous Effect)`**: (如：“驱逐强大邪祟”、“断肢重生（小概率奇迹）”、“获得神之洞察”、“抵御致命诅咒”)
    *   `instruction` -> **`仪式流程/圣水酿造法 (Ritual Procedure / Nectar Brewing Method)`**: 描述祈祷的步骤、祭品的摆放、咒语的吟诵，或圣水酿造的秘密。
    *   `origin`: 仪式/配方来源 (如：古代神庙壁画、先知的梦境启示、某神祇的直系祭司传承)
    *   `story`: 关于此仪式或圣物的著名传说，如某英雄通过它获得了弑神之力（夸张），或某王国因此免于瘟疫。

*   **特色设计:**
    *   **仪式多样性:** 不同神祇可能需要不同的仪式，有的需要盛大祭典，有的只需虔诚默祷。
    *   **圣水效果:** 可能有净化心灵、增强信仰、甚至暂时赋予某种神祇特性的效果。
    *   **护符功能:** 防御邪恶、吸引好运、增强特定神术效果。
    *   **神祇好感度:** 频繁使用某神祇相关的圣草和仪式，可能会提升该神祇的“好感度”，解锁更强的神恩或特殊剧情。反之，触犯禁忌则可能招致神罚。

**三、症候卡 -> 神罚预兆 (Omen of Divine Wrath) / 信仰动摇之兆 (Sign of Waning Faith)**

凡人因触怒神祇或信仰不坚而显现的负面状态。

*   **基础字段映射:**
    *   `category`: "神罚预兆" / "信仰动摇"
    *   `treatable_by_formulas` -> **`推荐赎罪仪式/安抚神祇之法 (Recommended Atonement Rituals / Appeasement Methods)`**: 关联祈祷仪式/神酿圣水。
    *   `atomic_syndromes` -> **`轻微神怒警告/信仰裂痕 (Minor Divine Warnings / Cracks in Faith)`**: 构成此状态的初期迹象。
    *   `symptoms` -> **`不祥表征 (Ominous Manifestations)`**: 关联具体的症状卡。
    *   `diseases` -> **`可能招致的灾厄 (Potential Calamities)`**: 关联更严重的神罚或邪神侵蚀卡。

*   **特色设计:**
    *   **命名方式:** 如“雷神之怒的低语”、“海神之叹息”、“智慧女神的沉默”、“大地母神的悲鸣”。
    *   **视觉表现:** 受影响者身上可能出现不祥的印记，周围环境发生微妙的异常（如乌云密布、动物不安）。

**四、症状卡 -> 诅咒印记 (Cursed Marks) / 神恩衰竭 (Grace Depletion)**

更具体、单一的负面神力影响或信仰缺失表现。

*   **基础字段映射:**
    *   `category`: "诅咒印记" / "神恩衰竭"
    *   `related_syndromes`: 关联的神罚预兆。
    *   `related_diseases`: 可能关联的强大邪神诅咒或神祇的直接惩罚。
    *   `symptom_type`: 状态类型 (如：“被圣光灼烧”、“力量流失”、“噩梦中听到神祇的低语”、“好运尽失”、“无法施展神术”)
    *   `severity`: 神罚/衰竭程度 (警告、惩戒、遗弃)
    *   `onset_time` -> **`触犯禁忌的时刻/信仰动摇的持续时间`**
    *   `location` -> **`神力主要作用点/信仰核心`** (如：“额头上的惩戒印记”、“灵魂中的圣光烙印”、“与神连接的通道”)
    *   `related_factors` -> **`渎神行为/诱惑之源`** (如：“亵渎神像”、“聆听邪神教唆”、“背弃誓言”)
    *   `relief_factors` -> **`忏悔行为/寻求宽恕`** (如：“向神庙献上珍贵祭品”、“完成神祇的试炼”、“得到高阶祭司的祝福”)

**五、疾病卡 -> 神祇之怒 (Wrath of a Deity) / 邪神侵蚀 (Corruption of an Elder Evil) / 信仰瘟疫 (Plague of Faithlessness)**

由神祇直接降下或因信仰崩溃导致的毁灭性灾难。

*   **基础字段映射:**
    *   `category`: "神祇之怒" / "邪神侵蚀" / "信仰瘟疫"
    *   `related_syndromes`: 前置的神罚预兆。
    *   `related_symptoms`: 典型的诅咒印记。
    *   `icd_code` -> **`神界灾厄等级/禁忌档案编号`** (万神殿的记录)
    *   `disease_type`: 灾难类型 (如：“宙斯的万雷天谴”、“波塞冬的滔天洪水”、“阿努比斯的

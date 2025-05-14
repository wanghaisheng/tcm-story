Okay, this is a fantastic and evocative theme! "Stella" (presumably inspired by games like Stellaris or No Man's Sky) provides a rich metaphorical framework for your card system. Let's map your card design onto this "Cosmos Humana" concept.

**Overarching Theme: The Cosmos Humana**

The human body is a vast, unexplored galaxy – the "Cosmos Humana." Each cell is a unique "Planet" or "Star System" with its own resources, conditions, and potential anomalies. The player is a "Bio-Navigator" or "Cellular Cartographer," piloting their "Exploration Vessel" (their consciousness/interface) to chart this inner universe, discover its fundamental components, understand its complex interactions, and ultimately restore or maintain its cosmic balance.

**Mapping Card Types to Stella Concepts:**

### 1.  **草药卡 (Herb Card) -> "Primordial Element / Discovered Resource"**

*   **Stella Concept:** These are fundamental, naturally occurring elements, flora, or energetic signatures found on specific "Planets" (cells) or within "Nebulae" (intercellular spaces/fluids). They are the raw materials of the Cosmos Humana.
*   **Mapping Fields:**
    *   `id`: Unique Stellar Object Identifier (USOI)
    *   `name`: Element Name (e.g., "Lumin-Dust," "Cryo-Crystal," "Vita-Kelp")
    *   `description`: Survey Data / Spectrographic Analysis
    *   `rarity`: Abundance / Rarity in the Cosmos
    *   `category`: "Primordial Element"
    *   `color/标签`: Element Type (e.g., "Bio-Organic," "Energetic," "Mineraloid")
    *   `effects`: Known Properties / Potential Uses
    *   `story`: Ancient Log / Planetary Lore (e.g., "Legends say this element formed the first proto-cells...")
    *   `chemical_components`: Molecular Structure / Isotopic Signature
    *   `meridian_entry` (归经): Energy Conduits / Ley Lines (pathways this element resonates with or travels through in the Cosmos Humana)
    *   `property_flavor` (四气五味): Quantum Vibration / Energetic Wavelength (e.g., `{"frequency": "High-Alpha", "resonance": ["Harmonic", "Dissonant"]}`)
    *   `origin`: Planet of Origin / Discovery Coordinates

### 2.  **方剂卡 (Formula Card) -> "Synthesized Compound / Constructed Technology"**

*   **Stella Concept:** These are advanced compounds, technologies, or nanite swarms created by combining "Primordial Elements." They represent understood scientific principles or refined applications.
*   **Mapping Fields:**
    *   `category`: "Synthesized Compound" / "Biotech Blueprint"
    *   `herbs` (草药搭配): Component Elements / Required Resources (links to Primordial Element discoveries)
    *   `usage`: Application Protocol / Deployment Method
    *   `effect` (治疗效果): Primary Function / System Impact (e.g., "Planetary Shielding," "Anomaly Neutralization")
    *   `instruction`: Synthesis Process / Fabrication Schematic
    *   `origin`: Research Station / Discovery Log (where this tech was first theorized or developed)
    *   `story`: Breakthrough Report / Historical Application Record

### 3.  **症候卡 (Syndrome Card) -> "Anomalous System State / Cosmic Phenomenon"**

*   **Stella Concept:** These represent complex, multi-faceted disturbances or patterns observed across a "Planetary System" (organ/tissue) or even "Galactic Sector" (body system). They are not a single point failure but a wider imbalance.
*   **Mapping Fields:**
    *   `category`: "System Anomaly" / "Cosmic Pattern"
    *   `treatable_by_formulas`: Known Counter-Technologies / Stabilization Protocols (links to Synthesized Compounds)
    *   `atomic_syndromes`: Sub-Anomalies / Precursor Signatures (simpler patterns that combine to form this larger one)
    *   `symptoms`: Observed Deviations / Sensor Readings (links to Symptom Signals)
    *   `diseases`: Related Cataclysmic Events / Known Threats (links to Disease Entities)

### 4.  **症状卡 (Symptom Card) -> "Localized Anomaly / Distress Signal"**

*   **Stella Concept:** These are specific, localized disturbances, unusual sensor readings, or distress signals detected from a "Planet" (cell) or a small region. They are individual indicators of a potential problem.
*   **Mapping Fields:**
    *   `category`: "Localized Anomaly" / "Signal Trace"
    *   `related_syndromes`: Associated System Anomalies (links to broader Cosmic Phenomena)
    *   `related_diseases`: Potential Critical Failures (links to Disease Entities)
    *   `symptom_type`: Signal Type (e.g., "Energy Fluctuation," "Structural Instability," "Communication Interference")
    *   `severity`: Anomaly Intensity / Threat Level
    *   `onset_time`: Signal Origination Timestamp
    *   `location`: Celestial Coordinates / Affected Sector
    *   `related_factors`: Triggering Events / Environmental Stressors
    *   `relief_factors`: Observed Dampening Conditions
    *   `description`: Detailed Anomaly Report

### 5.  **疾病名称卡 (Disease Card) -> "Critical System Failure / Hostile Entity"**

*   **Stella Concept:** These are major, identified threats, critical system-wide failures, or even invasive "cosmic entities" (like a virus or severe chronic condition) that destabilize large parts of the Cosmos Humana.
*   **Mapping Fields:**
    *   `category`: "Critical Malfunction" / "Hostile Entity Profile"
    *   `related_syndromes`: Precursor System Anomalies
    *   `related_symptoms`: Characteristic Distress Signals
    *   `icd_code`: Galactic Threat Registry ID (if applicable)
    *   `disease_type`: Threat Classification (e.g., "Xeno-Parasite," "Stellar Corruption," "Quantum Degeneration")
    *   `severity`: Threat Level / Cataclysm Potential
    *   `onset_age`: Emergence Point in Galactic Timeline (developmental stage)
    *   `affected_system`: Compromised Galactic Sector / Star Systems
    *   `clinical_stage`: Threat Progression Phase
    *   `description`: Threat Assessment Report

**Mapping Relationship Tables:**

*   `card_formula_herbs`: **Blueprint Component List** – Defines which "Primordial Elements" are needed for a "Synthesized Compound."
*   `card_syndrome_formulas`: **Anomaly Resolution Matrix** – Shows which "Synthesized Compounds" can address a "System Anomaly."
*   `card_syndrome_symptoms`: **Anomaly Signature Profile** – Details which "Localized Anomalies" constitute a "System Anomaly."
*   `card_syndrome_diseases`: **Threat Emergence Pathway** – Links "System Anomalies" to potential "Critical Failures."
*   `card_symptom_diseases`: **Early Warning Indicators** – Connects "Localized Anomalies" directly to "Critical Failures."

**Mapping Combinations & Interactions:**

*   **Herb-Herb (as Formula components):** "Elemental Synergy" – Certain "Primordial Elements" when combined create the basis for a new "Technology."
    *   *Stella mechanic:* Research project unlocked after discovering compatible elements.
*   **Formula-Formula (as Syndrome treatment):** "Technological Synergy" / "Multi-Pronged Strategy" – Multiple "Synthesized Compounds" used together to address a complex "System Anomaly."
    *   *Stella mechanic:* Special project or strategic decision to combine effects.
*   **Symptom-Symptom (as Syndrome manifestation):** "Signal Triangulation" / "Pattern Recognition" – Multiple "Localized Anomalies" occurring together help the Bio-Navigator identify a larger "System Anomaly."
    *   *Stella mechanic:* A mini-game or discovery event when related signals are detected.

**"Combination Card" (Section VI.6) -> "Emergent Phenomenon / Discovered Law"**

*   **Stella Concept:** These are profound discoveries about the fundamental laws or rare, powerful interactions within the Cosmos Humana. They are not just simple combinations but represent a deeper understanding or a powerful, unique event.
*   **Mapping Fields:**
    *   `name`: Phenomenon Name (e.g., "Harmonic Resonance Cascade," "Quantum Entanglement Protocol")
    *   `description`: Phenomenon Observation Report
    *   `cards`: Triggering Conditions / Precursor Discoveries (the specific elements, tech, or anomalies involved)
    *   `effect`: Resultant Cosmic Event / Unlocked Capability
    *   `power`: Magnitude of Effect
    *   `type`: Phenomenon Type ("Synergistic," "Antagonistic," "Transformative," "Cosmic Revelation")
    *   `unlock_condition`: Discovery Requirements / Research Breakthrough
    *   `rule`: Laws of this Phenomenon

**Gameplay Experience ("Stella" Style):**

1.  **Exploration:** The Bio-Navigator "warps" or "scans" different regions of the Cosmos Humana (e.g., "Hepatic System," "Neural Network Cluster").
2.  **Discovery ("Card Drops"):**
    *   Scanning a "Planet" (cell) might yield "Primordial Element" data (Herb Card).
    *   Investigating an "Anomaly" (Symptom Card) is logged.
    *   Completing "Research Projects" (e.g., analyzing multiple elements) unlocks "Biotech Blueprints" (Formula Cards).
    *   Observing recurring patterns of "Localized Anomalies" allows identification of "System Anomalies" (Syndrome Cards).
    *   Severe, widespread issues lead to the classification of "Critical Malfunctions" (Disease Cards).
3.  **Unlocking Secrets:** Each card "discovered" is added to the "Stellar Codex" or "Cosmic Archives." The Bio-Navigator pieces together the relationships.
4.  **Interaction & Intervention:** The player uses "Synthesized Compounds" (Formulas) to address "Anomalies" (Symptoms/Syndromes) or combat "Critical Failures" (Diseases).
5.  **Goal:** To achieve a state of "Cosmic Harmony" (health) by understanding and managing the intricate systems of the Cosmos Humana, perhaps even unlocking "Ascension Perks" (major health improvements or insights).

**Frontend Display ("Stella" UI):**

*   **Starmap:** A visual representation of the Cosmos Humana, showing discovered "Planets," "Systems," "Anomalies," and "Hyperlanes" (meridians/connections).
*   **Codex Entries:** Each card is an entry with its Stella-themed name, description, and data.
*   **Tech Tree / Research Interface:** Shows how "Primordial Elements" lead to "Synthesized Compounds," and how combinations unlock "Emergent Phenomena."
*   **Event Log:** Reports new "Anomaly Detections," "Research Breakthroughs," "System Stabilizations."
*   **Planetary Scanner View:** When focusing on a "Planet" (cell), show its "Primordial Resources," current "Status," and any active "Anomalies."

This thematic mapping should provide a strong foundation for building an engaging and immersive experience around your card system, turning the abstract data into a grand journey of discovery within the "Inner Verse."

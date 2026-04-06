# Hybrid Knowledge Graph & Vector Search Comparison for Real Estate

A comprehensive comparison framework for evaluating **5 hybrid retrieval configurations** combining Neo4j Knowledge Graphs and FAISS vector search for a real estate property recommendation system.

## 📋 Overview

This project implements and compares different hybrid retrieval strategies for answering two types of queries:
- **Residency Queries**: Flat/property-specific searches (floor, price, wing, facing, type)
- **Project Queries**: Project-level information (amenities, connectivity, RERA, payment plans)

### Key Finding
**Config 4 (Vector=Project Details, KG=Residency)** is the optimal configuration with:
- **Residency Accuracy**: 4.82/5 [EXCELLENT]
- **Project Relevance**: 4.60/5 [EXCELLENT]
- **Overall Score**: 4.71/5

## 🗂️ Repository Contents

### Files Included
- **Rohit-Gotecha-Hybrid-KG-Vector-Comparison.ipynb** - Main comparison notebook
- **Jalaram_Residency.csv** - Property/flat data (504 properties across 9 wings)
- **Project details.csv** - Project Q&A data (150 Q&A pairs)

### Notebook Structure

| Cell | Section | Description |
|------|---------|-------------|
| 1-3 | Setup | Install packages, import libraries, mount Google Drive |
| 4 | Data Loading | Load CSV files (residency and project details) |
| 5-16 | EDA | Exploratory data analysis with visualizations |
| 17-20 | Configuration | Neo4j and Gemini API setup |
| 21-24 | Infrastructure | Build KG, vector indexes, and search functions |
| 25-31 | Testing | Run 20 test questions across 5 configurations |
| 32-40 | Analysis | Generate accuracy and relevance charts |
| 41-42 | Scoring | Manual scoring input for final evaluation |
| 43 | Summary | Results and recommendations |

## 🔧 Configuration Modes

### 5 Hybrid Retrieval Strategies

```
┌─────────────┬──────────────────────┬──────────────────────┐
│  Config     │  Vector Search       │  Knowledge Graph     │
├─────────────┼──────────────────────┼──────────────────────┤
│ Config 1    │ Both CSVs             │ None                 │
│ Config 2    │ None                  │ Both CSVs            │
│ Config 3    │ Residency CSV         │ Project CSV          │
│ Config 4    │ Project CSV           │ Residency CSV        │
│ Config 5    │ Both CSVs             │ Both CSVs            │
└─────────────┴──────────────────────┴──────────────────────┘
```

### Performance Summary

| Config | Residency | Project | Average | Rating | Speed |
|--------|-----------|---------|---------|--------|-------|
| Config 1 | 2.41/5 [POOR] | 4.47/5 [GOOD] | 3.44/5 | [OK] | Fast |
| Config 2 | 3.98/5 [OK] | 2.60/5 [POOR] | 3.29/5 | [OK] | Medium |
| Config 3 | 2.52/5 [POOR] | 2.82/5 [POOR] | 2.67/5 | [POOR] | Medium |
| **Config 4** | **4.82/5 [EXCELLENT]** | **4.60/5 [EXCELLENT]** | **4.71/5** | **[EXCELLENT]** | **Fast** |
| Config 5 | 3.96/5 [OK] | 4.07/5 [GOOD] | 4.01/5 | [GOOD] | Slow |

**Recommendation: Use Config 4 for production**

## 📊 Data Schema

### Jalaram_Residency.csv (504 properties)
```
Wing          - Building identifier (A-I)
Flat_ID       - Flat number (e.g., A-101)
Superbuitup_Area - Property size in sqft
Price (₹)     - Price in rupees
Floor         - Floor number (1-14)
Type          - Property type (3BHK / 4BHK)
Facing direction - Orientation (East/West/North/South)
Facing view   - View type (Highway/Garden/Internal Road)
Status        - Availability (Available/Booked)
```

### Project details.csv (150 Q&A pairs)
```
QID      - Question ID
Question - Project inquiry
Answer   - Project response
```

### Example Queries

**Residency Query**
```
"Show 3BHK flats in Wing A facing East with Garden View under 1.5 crore"
→ Config 4: Returns 5 matching flats from Wing A
```

**Project Query**
```
"What are the amenities available?"
→ Config 4: Lists Gym, Swimming Pool, Club House, Theater, Children Park
```

## 🚀 Getting Started

### Prerequisites
- Python 3.8+
- Google Colab or local Jupyter environment
- Neo4j Cloud database instance
- Google Gemini API key

### Setup Instructions

1. **Clone the repository**
   ```bash
   git clone https://github.com/rohitgotecha/hybrid-kg-vector-comparison.git
   cd hybrid-kg-vector-comparison
   ```

2. **Upload to Google Colab**
   - Download the `.ipynb` file
   - Upload to Google Colab
   - Upload CSV files to Google Drive

3. **Configure credentials** (Cell 17 in notebook)
   ```python
   NEO4J_URI = "your_neo4j_uri"
   NEO4J_USER = "neo4j"
   NEO4J_PASSWORD = "your_password"
   GEMINI_API_KEY = "your_gemini_key"
   ```

4. **Run cells sequentially**
   - Cells 1-4: Setup and data loading
   - Cells 5-16: EDA and visualization
   - Cell 25: Comparison test (20 questions across 5 configs)
   - Cells 32-40: Generate comparison charts

### Running a Quick Test

```python
# Test single question across all configs
quick_compare("What are the amenities available?")
```

## 📈 Analysis & Results

### Residency Query Performance

**Config 4 excels because:**
- KG provides exact matching on structured fields (floor, price, wing)
- Flat filtering rules are enforced (type, facing direction, view)
- Wing diversity and constraint satisfaction maximize accuracy

**Example Results** (Q: "Show 3BHK flats in Wing A facing East with Garden View")
```
Config 1: Returns 1 flat (limited diversity)
Config 2: Returns 5 flats (good diversity)
Config 3: Returns 1 flat (low diversity)
Config 4: Returns 5 flats (BEST - high diversity + all constraints met)
Config 5: Returns 5 flats (redundant results, slower)
```

### Project Query Performance

**Config 4 excels because:**
- Vector search finds semantic matches for amenities, connectivity questions
- KG fallback handles project-level queries
- Best balance of coverage and relevance

**Example Results** (Q: "What amenities are available?")
```
Config 1: Returns complete list (4.47/5) [GOOD]
Config 2: Returns basic list (2.60/5) [POOR]
Config 3: Returns basic list (2.82/5) [POOR]
Config 4: Returns complete list + context (4.60/5) [EXCELLENT]
Config 5: Returns verbose list (4.07/5) [GOOD]
```

## 🎯 Metrics Used

### Residency Queries (Flat-specific)
- **Wing Diversity**: Unique wings returned (max 9)
- **Floor Diversity**: Different floors returned
- **Facing Diversity**: Different directions (E/W/N/S)
- **View Diversity**: Different view types
- **Constraint Compliance**: % of flats matching query filters
- **Response Time**: Seconds to generate answer

### Project Queries (Project-level)
- **Answer Length**: Character count
- **Relevance Score**: Manual rating (1-5 scale)
- **Completeness**: % of expected information included
- **Response Time**: Seconds to generate answer

## 📝 Scoring Ranges

```
4.5 - 5.0 = [EXCELLENT] - Best configuration for this query type
4.0 - 4.4 = [GOOD]      - Strong performance with minor issues
3.0 - 3.9 = [OK]        - Acceptable but could be better
2.0 - 2.9 = [POOR]      - Significant issues or missing data
0.0 - 1.9 = [WRONG]     - Incorrect or irrelevant response
```

## 🔍 Key Insights

### Why Config 4 Wins

1. **Optimal Data Allocation**
   - Residency data → KG (perfect for structured queries)
   - Project data → Vector (perfect for semantic queries)

2. **Complementary Strengths**
   - KG: Exact matching, constraint satisfaction, complex filtering
   - Vector: Semantic similarity, intent understanding, fuzzy matching

3. **Balanced Performance**
   - No weak query type (both 4.6+/5)
   - Consistent accuracy across diverse queries
   - Fast response times

### Why Other Configs Underperform

- **Config 1**: Only Vector search misses structured constraints
- **Config 2**: Only KG misses semantic intent
- **Config 3**: Inverted assignment (wrong data in wrong system)
- **Config 5**: Redundant data slows response, adds complexity

## 🛠️ Technology Stack

| Component | Technology | Purpose |
|-----------|-----------|---------|
| Graph DB | Neo4j Cloud | Structured property/question storage |
| Vector Search | FAISS | Semantic similarity on project Q&A |
| Embeddings | Sentence-Transformers | Generate semantic vectors |
| LLM | Google Gemini | Generate natural language responses |
| Data Processing | Pandas | Handle CSV data |
| ML Framework | PyTorch | Underlying embeddings library |

## 📚 Usage Examples

### Example 1: Residency Query

```python
question = "Find available 3BHK under 1.5 crore in Wing B only"
answer = configurable_hybrid_recommendation(question, "vector_project_kg_residency")

# Returns: 5 available 3BHK flats from Wing B under ₹13.75 lakhs
```

### Example 2: Project Query

```python
question = "What is the possession date and RERA registration?"
answer = configurable_hybrid_recommendation(question, "vector_project_kg_residency")

# Returns: Possession in 2027, check with sales team for RERA details
```

### Example 3: Batch Testing

```python
# Test all 20 questions across all 5 configs
# Generates comparison_results dictionary with all answers
# Produces accuracy and relevance charts
# See Cell 25 in notebook
```

## 📊 Generated Outputs

### Charts & Visualizations
- **EDA Charts**: Market analysis, pricing, distribution
- **Performance Charts**: Wing diversity, response times
- **Accuracy Charts**: Residency scoring across configs
- **Relevance Charts**: Project scoring across configs
- **Combined Overview**: Side-by-side config comparison

### Data Exports
- `comparison_results` - All Q&A pairs with 5 config responses
- `residency_stats` - Flat query performance metrics
- `project_stats` - Project query performance metrics

## ⚙️ Configuration & Customization

### Modify Test Questions

Edit Cell 25:
```python
TEST_QUESTIONS = [
    {"q": "Your custom question", "type": "residency"},
    {"q": "Another question", "type": "project"},
]
```

### Adjust Scoring Criteria

Edit Cells 41-42:
```python
MANUAL_ACCURACY_SCORES = [4.0, 3.5, 2.0, 4.8, 3.9]  # Config 1-5
MANUAL_RELEVANCE_SCORES = [4.5, 2.6, 2.8, 4.6, 4.1]  # Config 1-5
```

### Change Embedding Model

Cell 24:
```python
embed_model = SentenceTransformer('your-model-name')
```

## 🐛 Troubleshooting

### Neo4j Connection Failed
- Verify URI, username, password
- Check network connectivity
- Ensure Neo4j instance is running

### CSV Files Not Found
- Verify file paths in Cell 4
- Check Google Drive mount in Cell 3
- Ensure filenames match exactly

### Gemini API Errors
- Validate API key format
- Check rate limits and quota
- Verify internet connectivity

### Out of Memory
- Reduce batch size in vector indexing
- Use smaller embedding model
- Process data in chunks

## 📖 Documentation

- **Notebook Markdown**: Detailed explanations in each cell
- **Code Comments**: Inline documentation for functions
- **Output Summaries**: Each section prints detailed statistics
- **Quick Compare**: `quick_compare()` function for ad-hoc testing

## 🔐 Security Notes

**⚠️ IMPORTANT: Never commit credentials**

- Remove actual Neo4j passwords before pushing
- Use environment variables:
  ```python
  import os
  NEO4J_PASSWORD = os.getenv("NEO4J_PASSWORD")
  GEMINI_API_KEY = os.getenv("GEMINI_API_KEY")
  ```


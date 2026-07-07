# NutriQ

AI-powered recipe discovery agent built on Elasticsearch Agent Builder.

## What It Does

NutriQ is an AI agent that searches recipes using natural language. You describe what you want—"quick high-protein vegan lunch under 400 calories"—and it finds matching recipes from 230,000+ options.

## Agent Tools

NutriQ is a single agent with 5 specialized ES|QL tools:

| Tool | Purpose | Example Query |
|------|---------|---------------|
| `filter_by_nutrition` | Calories, protein, carbs, time | "under 400 cal in 20 min" |
| `filter_by_dietary_tags` | Vegan, gluten-free, keto, paleo | "keto breakfast ideas" |
| `search_by_technique` | Grill, braise, sauté, bake | "grilling recipes" |
| `rank_by_popularity` | Most-rated, highest-rated | "most popular pasta" |
| `meal_plan_candidates` | 30 options for weekly planning | "dinner options for the week" |

## Files


agent/
├── payload_agent.json           # Agent v1 config
├── payload_agent_v2.json        # Agent v2 config (current)
├── payload_tool_esql.json       # Nutrition filter tool
├── payload_tool_dietary_tags.json
├── payload_tool_technique.json
├── payload_tool_popularity.json
├── payload_tool_meal_plan.json
└── mappings/
   ├── recipes_mapping.json     # Recipe index mapping
   ├── interactions_mapping.json
   └── recipe_popularity_mapping.json

## Setup

1. Create Elasticsearch indices using the mappings in `agent/mappings/`
2. Ingest recipe data (Food.com dataset from Kaggle)
3. Create tools in Kibana Agent Builder using `payload_tool_*.json`
4. Create agent using `payload_agent_v2.json`

## How Tools Work

Each tool is an ES|QL query with typed parameters:

json
{
 "id": "nutriq.filter_by_nutrition",
 "type": "esql",
 "configuration": {
   "query": "FROM nutriq-recipes | WHERE nutrition_calories <= ?max_
calories ...",
   "params": {
     "max_calories": { "type": "float" }
   }
 }
}

The agent's LLM parses user queries, selects the appropriate tool, maps parameters, and executes against Elasticsearch.

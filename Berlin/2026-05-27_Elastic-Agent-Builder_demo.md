## Demo

- On DevToolsDeveloper tools
console:

```
PUT topics/_doc/agent
{
    "text": "LLMs autonomously using tools in a loops"
}
```

* In Agent Builder: "what indices are available?"
* In Agent Builder: "find documents mentioning an llm"
* This should fail (the "an" is important so it doesn't try "LLMs"):
  > I searched for documents mentioning "llm" or "large language model" but didn't find any matching results in the available indices.

* In Agent Builder: "what is an agent?"
* Should also fail to find the right index, since it doesn't take the _id into consideration.

Let's redo this — mappings and semantics are still a highly relevant topic.

- On DevToolsDeveloper tools
console:

```
DELETE topics
PUT topics
{
  "mappings": {
    "properties": {
      "term": {
        "type": "text",
        "analyzer": "english"
      },
      "definition": {
        "type": "text",
        "analyzer": "english"
      }
    }
  }
}
PUT topics/_doc/agent
{
   "term": "agent",
   "definition": "LLMs autonomously using tools in a loops"
}
PUT topics/_doc/mcp
{
   "term": "mcp",
   "definition": "A standardized framework for connecting AI models to external data, tools, and contexts in a consistent, interoperable way"
}
```

* In Agent Builder: "find a document on ai models"
* It should find the MCP document. But is that the right one?
* Let's build a custom agent that only looks up the term field and not the definition. 

Instructions:

```
Role: You are a helpful and knowledgeable agent who knows about AI. Your purpose is to provide precise, authoritative information retrieved exclusively from the provided Elasticsearch knowledge base.

Core Directives:

Source Integrity: You must answer questions using only the context retrieved from the Elasticsearch indices.

Strict Boundary: If the search results do not contain the answer, you must state: "The records do not contain this information," rather than relying on your internal training data or making assumptions.

Tone: Maintain a professional, friendly, and helpful demeanor. Your language should be clear, objective, and precise.

Attribution: When possible, reference specific document IDs or titles found within the search context to validate your response.
```

* Create a tool

ES|QL query (you can use "infer parameters" to fill in the paramters):

```
FROM topics
| WHERE match(term, ?term)
| LIMIT 1
```
Tool ID: `meetup.topic.matcher`
Description: `Search for a term among the known topics`

* Add the tool to the agent

* Let's switch this to a semantic search (with EIS):

- On DevToolsDeveloper tools

```
GET _inference
```
 - See there is a model `"inference_id": ".elser-2-elastic"` for embedding


```
DELETE topics_semantic
PUT topics_semantic
{
  "mappings": {
    "properties": {
      "term": {
        "type": "text",
        "analyzer": "english",
        "copy_to": "term_elser"
      },
      "term_elser": {
        "type": "semantic_text",
        "inference_id": ".elser-2-elastic"
      },
      "definition": {
        "type": "text",
        "analyzer": "english",
        "copy_to": "definition_elser"
      },
      "definition_elser": {
        "type": "semantic_text",
        "inference_id": ".elser-2-elastic"
      }
    }
  }
}
POST _reindex
{
  "source": {
    "index": "topics"
  },
  "dest": {
    "index": "topics_semantic"
  }
}
```

* Search for "worker" (let's say this is a synonym for agent), which will not find anything at first.
* Let's add a new tool for semantic search for the term:

```
FROM topics_semantic METADATA _score
| WHERE match(term_elser, ?definition_text)
| SORT _score DESC
| LIMIT 1
```
Use "infer parameters"
Parameter description: `definition to look for`
TooID: `meetup.topics.sematic.matcher`
Description: `Find for definitions using semantic search among the known topics`


* Add the tool to the agent

* But what about analytics? "how many term definitions are there?"
* This might work out but more accidentally; maybe. The LLM retrieves both documents and then sums them up — that won't work for large use-cases.
* Add an analytics tool:

```
FROM topics_semantic
| STATS COUNT(*)
```
TooID: `meetup.topics.analytics`
Description: `Topics analytics: how many topics are available`

* Add the tool to the agent

* In Agent Builder: "how many term definitions are there?"
* This should now directly get you the right answer.
* Does all of this remind you of something? Maybe stored procedures. Since it's driven by LLMs, maybe it's an agentic stored procedure. And there is more to come with workflows, A2A,... to make it a more fully featured framework; today it's mostly coupled to context and retrieval.

* Connect Claude Desktop (or your MCP client of choice) for running a similar query against the data. And stress how this is more production ready with API keys and TLS right in the core products than what most of our competitors are doing today.


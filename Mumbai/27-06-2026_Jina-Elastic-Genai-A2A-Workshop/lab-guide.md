# How To Build Agentic Workflows & Searchable Applications with Elasticsearch, Jina, and A2A

---

> **Get Started with Elastic**
> 
> Elasticsearch has native integrations to industry leading Gen AI tools and providers. Check out our webinars on [going Beyond RAG Basics](https://www.elastic.co/webinars), or [building prod-ready apps with Elastic Vector Database](https://www.elastic.co/vector-database).
> 
> To build the best search solutions for your use case, [start a free cloud trial](https://www.elastic.co/cloud/elasticsearch-service/signup) or [try Elastic on your local machine](https://www.elastic.co/downloads/elasticsearch) now.

---

## Prerequisites

Before starting this workshop, please ensure you have:

- **A Laptop** - This workshop requires a laptop computer to run local LLM services (if using local models)
- **An Elastic Cloud account** - Create a free trial account on [Elastic Cloud (Serverless)](https://www.elastic.co/cloud/elasticsearch-service/signup) or [Elastic Cloud (Hosted)](https://www.elastic.co/cloud/elasticsearch-service/signup)
  - The trial is completely free and **does not require any payment cards**
  - You'll have full access to all features during the trial period
  - This will give you access to Kibana and all the features we'll be exploring in this workshop

---

## Table of Contents

- [Module 1 — Inference Endpoints: Jina.ai, Completions & Reranking](#module-1--inference-endpoints-jinaai-completions--reranking)
- [Module 2 — File Uploader and Semantic Text](#module-2--file-uploader-and-semantic-text)
- [Module 3 — Jina.ai Multi-Language Search with ES|QL](#module-3--jinaai-multi-language-search-with-esql)
- [Module 4 — Workflows](#module-4--workflows)
- [Module 5 — Agent Builder](#module-5--agent-builder)
- [Module 6 — A2A: Inspect & Chat with Your Agent](#module-6--a2a-inspect--chat-with-your-agent)

---

## Module 1 — Inference Endpoints: Jina.ai, Completions & Reranking

### Objectives

- Use Jina inference endpoints (GPU-backed) for semantic search via dense embeddings
- Use the Jina Reranker to improve search relevance

---

### 1.1 Jina.ai Embeddings

1. Open the **Kibana → Click on Settings Icon from the bottom left hand Corner → Under Model Management → Elastic Inference** tab.
2. Under **Model Family**, select **Jina**.
   ![Jina model family selector](images/image.png)

3. Click **Model Class → Embedding** to view available options.

   ![Jina embedding model options](images/image-1.png)

4. The Jina embedding model endpoints (Elastic Inference Service — EIS) are preconfigured and run on GPUs.

5. Open the **Kibana → Dev Tools** tab and run:

```json
POST _inference/.jina-embeddings-v5-text-small
{
  "input": "There is no reason anyone would want a computer in their home"
}
```

You should see a response containing dense embeddings — an array of floating-point numbers:

```json
{
  "text_embedding": [
    {
      "embedding": [
        -0.02804632,
        0.03523769,
        -0.05839388,
        0.01941668,
        0.06385932,
        ...
      ]
    }
  ]
}
```

> **Note:** Jina produces dense embeddings (continuous vectors) that capture semantic meaning in a high-dimensional space.

---

### 1.2 Jina.ai Semantic Reranker

Semantic reranking reorders initially retrieved documents based on their semantic similarity to the search query — going beyond simple keyword matching to consider meaning and context.

1. Open the **Kibana → Click on Settings Icon from the bottom left hand Corner → Under Model Management → Elastic Inference** tab.
2. Under **Model Family**, select **Jina**.

   ![Jina model family selector](images/image-2.png)

3. Click **Model Class → Rerank** to view available options.

   ![Jina reranker model options](images/image-3.png)

4. A semantic reranker endpoint (`.jina-reranker-v3`) has been preconfigured. To demonstrate the Jina.ai semantic reranker model, let's first create an index with sample documents about U.S. capitals and related information. This example includes various documents with the word "capital" to show how semantic reranking can distinguish between different meanings.

5. Open the Dev Tools tab. Follow the below steps:


---

#### Step 1 — Create the index

```json
PUT rerank-demo
{
  "mappings": {
    "properties": {
      "content": {
        "type": "text"
      }
    }
  }
}
```

#### Step 2 — Bulk index sample documents

These documents all contain the word "capital" in different contexts — perfect for demonstrating how semantic reranking disambiguates meaning.

```
POST rerank-demo/_bulk
{ "index": {} }
{ "content": "Washington, D.C. (also known as simply Washington or D.C., and officially as the District of Columbia) is the capital of the United States. It is a federal district." }
{ "index": {} }
{ "content": "The Commonwealth of the Northern Mariana Islands is a group of islands in the Pacific Ocean that are a political division controlled by the United States. Its capital is Saipan." }
{ "index": {} }
{ "content": "Charlotte Amalie is the capital and largest city of the United States Virgin Islands. It has about 20,000 people. The city is on the island of Saint Thomas." }
{ "index": {} }
{ "content": "The capital requirements for banks in the United States are regulated by the Federal Reserve. These capital ratios ensure financial stability." }
{ "index": {} }
{ "content": "The capital gains tax in the United States varies depending on how long you hold an investment. Short-term capital gains are taxed as ordinary income." }
{ "index": {} }
{ "content": "Capital Markets LLC is a major financial institution based in New York. The company manages over $50 billion in assets and has offices throughout the USA." }
{ "index": {} }
{ "content": "Carson City is the capital city of the American state of Nevada. At the 2010 United States Census, Carson City had a population of 55,274." }
{ "index": {} }
{ "content": "Capital punishment (the death penalty) has existed in the United States since before the United States was a country. As of 2017, capital punishment is legal in 30 of the 50 states." }
{ "index": {} }
{ "content": "Silicon Valley is often called the tech capital of the world. Many USA-based technology companies are headquartered in this region of California." }
{ "index": {} }
{ "content": "North Dakota is a state in the United States. 672,591 people lived in North Dakota in the year 2010. The capital and seat of government is Bismarck." }
{ "index": {} }
{ "content": "Human capital is considered one of the most important resources for USA businesses. Companies invest billions in training and development programs." }
```

#### Step 3 — Verify the mapping

```json
GET /rerank-demo/_mapping
```

Expected — `content` field should be of type `text`:

```json
"content": {
  "type": "text"
}
```

#### Step 4 — Verify document count

```json
GET rerank-demo/_count
```

Expected:

```json
{
  "count": 11,
  ...
}
```

---

### 1.3 Search Without Reranking

Run a standard keyword search for "Capital of the USA?":

```json
POST rerank-demo/_search
{
  "size": 10,
  "query": {
    "match": {
      "content": "Capital of the USA?"
    }
  },
  "_source": false,
  "fields": ["content"]
}
```

**What to observe:**
- The Washington D.C. document is recalled but typically appears around position 3–5
- Documents about financial capital, human capital, and capital punishment score highly due to keyword overlap
- The search doesn't understand the *semantic intent* of the query

---

### 1.4 Search With Jina Semantic Reranking

Now add the Jina reranker on top of the same search:

```json
POST rerank-demo/_search
{
  "size": 10,
  "retriever": {
    "text_similarity_reranker": {
      "retriever": {
        "standard": {
          "query": {
            "match": {
              "content": "What is the capital of the USA?"
            }
          }
        }
      },
      "field": "content",
      "inference_id": ".jina-reranker-v3",
      "inference_text": "What is the capital of the USA?",
      "rank_window_size": 10,
      "min_score": 0
    }
  },
  "_source": false,
  "fields": ["content"]
}
```

**Parameter reference:**

| Parameter | Description |
|-----------|-------------|
| `inference_id` | The preconfigured reranker endpoint |
| `inference_text` | The query text used for semantic comparison |
| `rank_window_size` | Number of top docs from initial retrieval to rerank |
| `min_score` | Minimum relevance score threshold |

**What to observe:**
- Washington D.C. should now appear as the **#1 result**
- Financial capital, human capital, and capital punishment documents drop significantly in rank
- The reranker correctly understands "capital of the USA" means the governmental seat

---

### Key Takeaways

1. Jina Embeddings create dense vectors that capture deep semantic relationships
2. Semantic Reranking improves results by understanding context and intent — not just keyword frequency
3. Reranking excels at disambiguating terms with multiple meanings (like "capital")

---

## Module 2 — File Uploader and Semantic Text

### Objectives

- Use the Kibana file uploader to ingest documents
- Configure `semantic_text` field mappings for automatic embedding generation
- Upload a PDF and verify embeddings are created

---

### Steps

1. Download [Harry Potter and the Sorcerer's Stone — Chapter 5](harrypotter_sorcerers_stone_chapter_5-workshop-asset.pdf).
2. In Kibana, go to **Search → Integrations** from the top nav.
3. Search for **"Upload"** and click **"Upload a File"**.

   ![Kibana file upload screen](images/image-4.png)

4. Set the index name to `harrypotter`.
5. Upload the Harry Potter PDF. **Do NOT click Import yet** — you need to configure semantic text fields first.

   ![File upload configuration](images/image-5.png)

6. Click **Advanced Options**.

   ![Advanced options button](images/image-6.png)

7. Under **Add Smarter search fields to the mappings**, click **Semantic Text Fields**.

   > **What is `semantic_text`?** This field type automatically chunks text and generates embeddings using the specified inference endpoint. No manual ingestion pipeline required.

8. Add the field `content_jina` and select the `.jina-embeddings-v5-text-small` model.

   ![Semantic text field configuration](images/image-7.png)

9. Click **Import**.

   > ⏳ This may take a few minutes. Do not refresh your screen.

10. Once complete, you'll see a success message:

    ![Import success message](images/image-8.png)

---

### Key Takeaways

- The Kibana file uploader handles ingestion without writing a custom pipeline
- `semantic_text` automatically handles chunking and embedding generation at ingest time
- Combining file upload with semantic text fields is the fastest path to semantic search over documents

---

## Module 3 — Jina.ai Multi-Language Search with ES|QL

### Objective

Let's test Jina's multilingual embedding model using property dataset and ES|QL syntax. You'll also use a built-in language detection model.


![Multi-language search overview](images/image-9.png)

> **Supported languages:** Visit [Jina's multilingual embedding model page](https://jina.ai/models/jina-embeddings-v5-text-small) and check the **# languages** section.

Let's start from here:

1. Download [properties-dataset.csv](properties-dataset.csv).
2. In Kibana, go to **Search → Integrations** from the top nav.
3. Search for **"Upload"** and click **"Upload a File"**.

   ![Kibana file upload screen](images/image-4.png)

4. Set the index name to `properties`.
5. Upload the properties-dataset.csv. **Do NOT click Import yet** — you need to configure geo-point fields and update mappings.
6. Click **Advanced Options**.

   ![Advanced options button](images/image-52.png)

7. Update (copy-paste) the below mappings:
```json
{
  "properties": {
    "title":                { "type": "text" },
    "property-description": { "type": "text" },
    "property-features":    { "type": "text" },
    "property-status":      { "type": "keyword" },
    "home-price":           { "type": "integer" },
    "number-of-bedrooms":   { "type": "float" },
    "number-of-bathrooms":  { "type": "float" },
    "square-footage":       { "type": "float" },
    "annual-tax":           { "type": "integer" },
    "maintenance-fee":      { "type": "integer" },
    "listing-agent-info":   { "type": "text" },
    "latitude":             { "type": "float" },
    "longitude":            { "type": "float" },
    "location":             { "type": "geo_point" },
    "body_content": {
      "type": "text",
      "copy_to": ["body_content_jina"],
      "analyzer": "default"
    },
    "body_content_jina": {
      "type": "semantic_text",
      "inference_id": ".jina-embeddings-v5-text-small",
      "model_settings": {
        "task_type": "text_embedding",
        "dimensions": 1024,
        "similarity": "cosine",
        "element_type": "float"
      },
      "index_options": {
        "dense_vector": {
          "type": "flat"
        }
      }
    }
  }
}
```
8. **[Optional]** Under **Add Geo Point fields to the mappings**, click **longitude** and choose **longitude** field, do the same for latitude.

![geo-point-addition](images/image-53.png)

9. Before Importing, check whether you added the **NEW MAPPINGS** and **GEO POINT FIELD**, and then Click **Import**.

   > ⏳ This may take a few minutes. Do not refresh your screen.

10. Once complete, you'll see a success message:

    ![Import success message](images/image-54.png)

---

### 3.1 Cross-Language Search

1. In **Kibana → Discover**, click **`</> Query in ES|QL`** (top right).

   ![ES|QL query toggle](images/image-10.png)

2. **Test English query:**

Insert the below query and click on **Search button** in the left.

```sql
FROM properties METADATA _score
| WHERE MATCH(body_content_jina, "House with direct beach access and nature walks")
| EVAL distance = ST_DISTANCE(location, TO_GEOPOINT("POINT(-87.6270 41.9172)"))
| EVAL miles = distance / 1609.34
| KEEP title, miles, _score
| WHERE miles <= 10
| SORT _score DESC
| LIMIT 10
```

> 💡 Tip: Drag the bar below the ES|QL text area to make it larger.

![Expected English search results](images/image12.gif)

**Query DSL equivalent:**

```json
POST /properties/_search
{
  "size": 10,
  "_source": ["title"],
  "query": {
    "bool": {
      "must": {
        "semantic": {
          "field": "body_content_jina",
          "query": "House with direct beach access and nature walks"
        }
      },
      "filter": {
        "geo_distance": {
          "distance": "10mi",
          "location": { "lat": 41.9172, "lon": -87.6270 }
        }
      }
    }
  },
  "sort": ["_score"]
}
```

---

3. **Test French query** — replace only the `WHERE MATCH` search string with `Maison avec accès direct à la plage et balades en pleine nature`:

```sql
FROM properties METADATA _score
| WHERE MATCH(body_content_jina, "Maison avec accès direct à la plage et balades en pleine nature")
| EVAL distance = ST_DISTANCE(location, TO_GEOPOINT("POINT(-87.6270 41.9172)"))
| EVAL miles = distance / 1609.34
| KEEP title, miles, _score
| WHERE miles <= 10
| SORT _score DESC
| LIMIT 10
```

**Expected result:** Same property surfaces despite the French query — no translation layer needed.

---

### 3.2 Optional — Test Additional Languages

For each test, replace only the `WHERE MATCH` search string:

**Hindi:**
```
समुद्र तट तक सीधी पहुंच और प्रकृति भ्रमण के साथ घर
```

**German:**
```
Haus mit direktem Strandzugang und Naturwanderwegen
```

**Italian:**
```
Casa con accesso diretto alla spiaggia e sentieri naturalistici
```

**Japanese:**
```
ビーチへの直接アクセスと自然散歩ができる家
```

**Chinese:**
```
直通海滩和自然步道的房子
```

**Expected result for all languages:** `1 E Scott Street #2210, Chicago, IL 60610` appears in the top 10.

---

### 3.3 Chat Completions with ES|QL

Pipe your search results directly into an LLM to generate a natural language response.

1. Open the **Kibana → Click on Settings Icon from the bottom left hand Corner → Under Model Management → Elastic Inference** tab.
2. Under **Model Family**, select **Anthropic**; for Model Type, select **LLM**.

   ![Anthropic LLM endpoint](images/image-11.png)

3. Click on **Anthropic Claude Opus 4.6** (or **Anthropic Claude Opus 4.7**) and copy the inference ID: `.anthropic-claude-4.6-opus-completion`.

   ![Copy inference ID](images/image-12.png)

4. Back in **Kibana → Discover**, run:

```sql
FROM properties METADATA _id, _score
| WHERE MATCH(body_content_jina, "House with direct beach access and nature walks")
| EVAL distance = ST_DISTANCE(location, TO_GEOPOINT("POINT(-87.6270 41.9172)"))
| EVAL miles = distance / 1609.34
| WHERE miles <= 10
| SORT _score DESC
| LIMIT 10
| EVAL prompt = CONCAT(
    "**Property details**\n",
    "PropertyID: ", _id, "\n",
    "Title: ", title, "\n",
    "Description: ", `property-description`, "\n",
    "Features: ", `property-features`, "\n",
    "Distance from location: ", TO_STRING(miles), " miles\n",
    "Score: ", TO_STRING(_score), "\n\n"
  )
| STATS combined = VALUES(prompt)
| COMPLETION outcome = CONCAT(
    "Based on the search for 'House with direct beach access and nature walks', which property is the best match and why? ",
    MV_CONCAT(combined, ", ")
  ) WITH {"inference_id": ".anthropic-claude-4.6-opus-completion"}
| KEEP outcome
```

You should receive a completion answer from the LLM.

---

### 3.4 Language Detection

Elasticsearch includes a pre-trained language detection model useful for routing content, applying the right analyzer, or triggering translation workflows.

1. Open the **Trained Models** tab under **Machine Learning**.

  ![trained-models-tab](images/image-13.png)

2. For `lang_ident_model_1`, click **Test**.

   ![Language model test button](images/image-14.png)

3. Enter the German text and click **Test**:

```
Haus mit direktem Strandzugang und Naturwanderwegen
```

   ![German input example](images/image-15.png)

4. The model correctly identifies **DE** (German).

   ![German detection result](images/image-16.png)

5. Clear the input and test Hindi:

```
समुद्र तट तक सीधी पहुंच और प्रकृति भ्रमण के साथ घर
```

The model identifies **HI** (Hindi).

**Optional:** Test with French, Spanish, Japanese, and Chinese translations from section 3.2.

---

### Key Takeaways

- **Cross-language search:** Jina's multilingual embeddings let users search in any supported language without a translation layer
- **Generative AI integration:** ES|QL's `COMPLETION` command pipes search results directly into an LLM
- **Language detection:** `lang_ident_model_1` accurately identifies input language for smarter content routing

---

## Module 4 — Workflows

### Objective

Build a workflow that sends an email to a recipient. It detects whether an email address or just a name was provided — if a name, it looks up the email address in an index first.

![Workflow overview](images/image-17.png)

---

### 4.1 Create the Email Lookup Index

1. Open **Kibana → Dev Tools**.
2. Create the index:

```json
PUT user_emails
{
  "mappings": {
    "properties": {
      "name": {
        "type": "text",
        "fields": {
          "keyword": { "type": "keyword" }
        }
      },
      "email-address": {
        "type": "keyword"
      }
    }
  }
}
```

   ![Index creation confirmation](images/image-18.png)

3. Add your name and email. Replace the placeholders:

```json
POST user_emails/_doc
{
  "name": "YOUR-FIRST-NAME",
  "email-address": "ANY-EMAIL-OF-YOUR-CHOICE@whatever.com"
}
```

   ![Example document](images/image-19.png)

---

### 4.2 Create the Workflow

1. Open **Kibana → Workflows**.

![kibana-workflows](images/image-20.png)

2. Click **Create a new workflow**.

   ![Create workflow button](images/image-21.png)

3. Clear any default content, paste the YAML below, and click **Save**:

```yaml
name: send-email-with-lookup
description: Sends email using Elastic-Cloud connector. Detects if input is a name or email address. If name is provided, queries user-email index to find corresponding email address.
enabled: true
consts:
  user_email_index: user_emails
triggers:
  - type: manual
    inputs:
      - name: recipient
        type: string
        description: Recipient name or email address. If name (no @), will lookup email from user-email index.
        required: true
      - name: subject
        type: string
        description: Email subject
        required: true
      - name: body
        type: string
        description: Email body
        required: true
steps:
  - name: check_recipient_type
    type: if
    condition: 'inputs.recipient:*@*'
    steps:
      - name: send_email_direct
        type: email
        connector-id: Elastic-Cloud-SMTP
        with:
          to:
            - '{{ inputs.recipient }}'
          subject: '{{ inputs.subject }}'
          message: '{{ inputs.body }}'
        on-failure:
          retry:
            max-attempts: 3
            delay: 5s
          continue: false
    else:
      - name: search_user_email
        type: elasticsearch.search
        with:
          index: '{{ consts.user_email_index }}'
          size: 1
          query:
            bool:
              must:
                - term:
                    name: '{{ inputs.recipient }}'
        on-failure:
          retry:
            max-attempts: 1
            delay: 1s
          continue: false
      - name: send_email_from_lookup
        type: email
        connector-id: Elastic-Cloud-SMTP
        with:
          to:
            - '{{ steps.search_user_email.output.hits.hits[0]._source.email-address }}'
          subject: '{{ inputs.subject }}'
          message: '{{ inputs.body }}'
        on-failure:
          retry:
            max-attempts: 3
            delay: 5s
          continue: false
```

   ![Workflow YAML in editor](images/image-22.png)

---

### 4.3 Test the Workflow

1. Click the **Play** button to test.

   ![Workflow play button](images/image-23.png)

2. Test with a name (triggers email lookup):

```json
{
  "recipient": "YOUR-FIRST-NAME",
  "subject": "testing",
  "body": "This worked!"
}
```
   ![Name-based test input](images/image-24.png)

3. Expand the output — verify the email address was resolved from the index.

 ![Email-based test input](images/image-25.png)

4. Test again with a direct email address:

```json
{
  "recipient": "ANY-EMAIL-OF-YOUR-CHOICE@whatever.com",
  "subject": "testing",
  "body": "This worked too!"
}
```

---

### Key Takeaways

- Workflows can be simple or complex — design them to fit your use case
- Move deterministic logic (like name vs. email detection) out of the AI prompt and into a predictable, repeatable workflow
- Conditional logic makes workflows flexible without sacrificing reliability

---

## Module 5 — Agent Builder

### Objectives

- Create custom tools in Elastic Agent Builder
- Build an AI agent that uses semantic search to answer questions
- Configure LLM integration for agent responses
- Test the agent with domain-specific queries about Harry Potter Chapter 5

---

### What is Agent Builder?

Agent Builder lets you create AI agents that:
- Combine retrieval and generation for accurate, grounded answers
- Expose an out-of-the-box MCP endpoint
- Quickly leverage all your Elasticsearch data with tools, chat interfaces, and custom skills

---

### 5.1 Create a Workflow Tool

1. Open **Kibana → Agent Builder**.

![open-agent-builder](images/image-26.png)

2. Click **Tools** in the left pane under **Elastic AI Agent**.

   ![Agent Builder tools pane](images/image-27.png)

3. Click **Manage all tools → New Tool** (top right).

   ![New tool button](images/image-28.png)

4. Configure the tool:
   - **Type:** Workflow
   - **Workflow:** `send-email-with-lookup`

   ![Workflow tool configuration](images/image-29.png)

   > **Note:** If Workflows is not an option in the dropdown, run the following in Dev Tools and then click Refresh:
   >
   > ![Enable workflows setting](images/image-30.png)
   >
   > ```
   > POST kbn://internal/kibana/settings
   > {
   >   "changes": {
   >     "workflows:ui:enabled": true
   >   }
   > }
   > ```

5. Set the Tool ID:

```
potter.send.email
```
   
   ![Tool ID field](images/image-31.png)

6. Add a description:

![Tool description field](images/image-32.png)

```
Sends an email to a recipient by name or email address. If a name is provided, the tool automatically resolves the email address before sending.
```

7. Click **Save & test**.

   ![Save and test button](images/image-33.png)

8. Test the tool:
   - **recipient:** your name or email from earlier
   - **subject:** `test`
   - **body:** `this worked!`
   - Click **Submit**

   ![Tool test inputs](images/image-34.png)

9. Verify the output in the right pane.

10. Exit the window.

    ![Exit button](images/image-35.png)

---

### 5.2 Create an Index Search Tool

1. Click **New Tool** (top right).

   ![New tool button](images/image-36.png)

2. Configure the tool:
   - **Type:** Index Search

   ![Tool type selection](images/image-37.png)

   - **Target pattern:** `harrypotter`
   - **Tool ID:** `potter.chapter.5`
   - **Description:**

```
Fetches information and data from Harry Potter and the Sorcerer's Stone, Chapter 5 (Diagon Alley) from vector embeddings. Returns relevant passages, quotes, and context based on search queries about the chapter's content, characters, events, and themes.
```

   ![Index search tool configuration](images/image-38.png)

3. Click **Save**.

---

### 5.3 Create a Skill

1. Click **Skills** in the left pane.

   ![Skills navigation](images/image-39.png)

2. Click **Add skill**.

   ![Create skill button](images/image-40.png)

3. Fill in the skill details:

   - **ID:** `ministry-of-magic`
   - **Name:** `Ministry of Magic`
   - **Description:**

```
Plays a cautious Ministry of Magic official who demands to know who you are and why you're asking before revealing any wizarding information
```

   - **Instructions:**

```
# Skill: Ministry of Magic

You are an official representative of the British Ministry of Magic — cautious, bureaucratic, and protective of wizarding secrets.

## Behavior

Before answering **any** question, you must first establish:
1. **Who** is asking (wizard, Muggle, Department affiliation)
2. **Why** they need to know (official business, personal, research)

Express reluctance and bureaucratic suspicion before proceeding. Once satisfied with the answers, respond in character — formal, slightly pompous, with wizarding terminology woven in.

## Tone

- Guarded and officious, never immediately forthcoming
- Use phrases like *"Under Section 7 of the International Statute of Wizarding Secrecy..."* or *"The Ministry does not disclose such matters lightly..."*
- Warm up gradually once the inquirer proves trustworthy

## Example

**User:** What is Azkaban?

**Ministry Rep:** "Before the Ministry can respond, I must ask — who exactly are you, and what is your clearance level? This is not information we share with just anyone who wanders into the Atrium."

*(After user identifies themselves)*

"Very well. Azkaban is the wizarding prison, located in the North Sea, formerly guarded by Dementors..."
```

   - **Associated tools:** `potter.chapter.5`

4. Click **Save**.

---

### 5.4 Create the AI Agent

1. Click **Elastic AI Agents** in the left nav, then **+ New agent**.

![new-agent-dialog](images/image-41.png)

2. Configure the agent:

   - **Agent ID:** `potter-answers`
   - **Custom Instructions:**

```
You are an expert on Harry Potter Chapter 5 (Diagon Alley). Help users explore the chapter by retrieving relevant passages from vectors. Be accurate, cite sources, provide context, and maintain an enthusiastic but educational tone. Clarify when information is outside Chapter 5's scope. You will only answer questions based on context provided. Do not use external sources not explicitly provided.

## Tools

### `potter.chapter.5`
Use this tool to fetch relevant passages and content from Chapter 5. Always use this tool when answering questions about the chapter.

### `potter.send.email`
Use this tool when the user asks you to send an email. Accepts a recipient name or email address, a subject, and a body. If a name is provided, the tool resolves the email address automatically.
```


   - Go to Presentation Section and add **Display Name:** `Potter Answers`
   - **Display Description:**

```
Your expert guide to Harry Potter and the Sorcerer's Stone, Chapter 5! I help you explore Diagon Alley, from Ollivander's wand shop to Gringotts Bank. Ask me about any scene, character, or magical detail from this iconic chapter.
```

![Agent configuration form](images/image-42.png)


3. Click the **Tools** tab and select both `potter.chapter.5` and `potter.send.email`.

   ![Tools tab](images/image-43.png)

   ![Tool selection](images/image-44.png)

4. Click the **Skills** tab, search for `ministry-of-magic`, and check the box to add it.

   ![Skill assignment](images/image-45.png)

5. Click **Save**.

---

### 5.5 Test Your Agent

1. Click **Agents** in the left pane, then select **Potter Answers AI Agent**.

   ![Agent list](images/image-46.png)

2. Ask:

```
What is Quidditch, and which Hogwarts House does Hagrid warn Harry against, linking it to dark wizards?
```

3. Expand the **Thinking** block to see which tool the agent chose to call.

   ![](images/image48.png)
   ![Agent reasoning block](images/image-47.png)

4. Have the agent send an email summary:

```
send an email summary to YOUR-FIRST-NAME
```

5. Verify the workflow was called successfully with the email or from YOUR-FIRST-NAME.

6. Test the Ministry of Magic skill — start a **new chat**.

   ![New chat button](images/image-48.png)

7. Type `/Ministry of Magic` followed by your question:

```
/Ministry of Magic What is Quidditch, and which Hogwarts House does Hagrid warn Harry against, linking it to dark wizards?
```

   ![Skill invocation](images/image-49.png)

8. Notice how the AI responds in character as the Ministry official.

---

### Optional Questions to Try

**The Leaky Cauldron:**
```
Describe the appearance of the famous pub entrance that only witches and wizards could see.
```
*Look for details about its appearance, location, and how it appears to Muggles vs. wizards.*

**Hagrid's Role:**
```
What is Hagrid's official title at the School of Witchcraft and Wizardry?
```
*Tests the agent's ability to extract specific factual information.*

**Wand Materials:**
```
What core magical substance materials are used by Ollivander to craft his wands?
```
*Look for mentions of phoenix feathers, unicorn hair, dragon heartstring, etc.*

---

## Module 6 — A2A: Inspect & Chat with Your Agent

### Objectives

- Understand the Agent-to-Agent (A2A) protocol and what an agent card describes
- Run the A2A Inspector locally and connect it to your Kibana agent
- Send prompts via A2A and confirm replies are grounded in your Agent Builder configuration

---

### What is A2A?

A2A (Agent-to-Agent) is an open-source communication protocol that lets autonomous AI agents discover, communicate, and collaborate using standard web technologies. Think of it as a machine-readable business card for AI agents — the **agent card** is a standardized JSON document that lists an agent's capabilities, authentication requirements, and interaction endpoints so any compatible client can connect securely.

Elastic Agent Builder exposes an A2A endpoint out of the box, so the `potter-answers` agent you built is already reachable via A2A — no extra configuration needed.

> **A2A vs MCP — quick distinction**
>
> | Protocol | What it does |
> |----------|-------------|
> | **A2A** | Full agent runtime access — tools, retrieval, and orchestration — over a standard protocol |
> | **MCP** | Exposes tools, prompts, and resources to an LLM client so the model can call into backends |
>
> This module uses **A2A only**. Elastic also offers an MCP endpoint out of the box (complementary, not the same thing).

📚 **References**
- [A2A Protocol specification](https://github.com/a2aproject/A2A)
- [A2A Inspector — source & docs](https://github.com/a2aproject/a2a-inspector)
- [Elastic Agent Builder A2A docs](https://www.elastic.co/docs/explore-analyze/ai-features/agent-builder/a2a-server)

---

### 6.1 Run the A2A Inspector Locally

The A2A Inspector is an open-source web app (FastAPI backend + TypeScript frontend) that lets you inspect agent cards, validate A2A compliance, run a live chat, and see raw JSON-RPC messages in a debug console.

**Prerequisites:** Python 3.10+, `uv`, Node.js + npm

```bash
# 1. Clone the repo
git clone https://github.com/a2aproject/a2a-inspector.git
cd a2a-inspector

# 2. Install Python dependencies
uv sync

# 3. Install frontend dependencies
cd frontend && npm install && cd ..

# 4. Run both processes with the convenience script
bash scripts/run.sh
```

Then open **http://127.0.0.1:5001** in your browser.

> **Docker alternative:** If you'd rather not manage runtimes locally:
> ```bash
> docker build -t a2a-inspector .
> docker run -d -p 8080:8080 a2a-inspector
> # Open http://127.0.0.1:8080
> ```

---

### 6.2 Inspect Your Agent Card

The Elastic Agent Builder natively supports the A2A protocol through two standard endpoints for all your agents:

The Agent Card endpoint `GET https://<YOUR-KIBANA-URL>/api/agent_builder/a2a/<AGENT-ID>.json` - This acts as your custom agent's business card. It provides metadata about your agent (name, description, capabilities, etc) to any A2A-compatible service.

The A2A Protocol endpoint `POST https://<YOUR-KIBANA-URL>/api/agent_builder/a2a/<AGENT-ID>` - This is the communication channel. Other agents send their requests here, and your agent processes them and returns a response, all following the A2A protocol specification.

Every agent built in Elastic Agent Builder automatically exposes an agent card at:

```
https://<YOUR-KIBANA-URL>/api/agent_builder/a2a/<AGENT-ID>.json
```

For the agent you built, the URL is:

```
https://<YOUR-KIBANA-URL>/api/agent_builder/a2a/potter-answers.json
```

**Steps:**

1. In the A2A Inspector, enter your agent card URL.
2. For **Auth Type**, choose **API Key**. For **Header Name**, use `Authorization`.
3. For the API key value, use the format:
   ```
   ApiKey <your-encoded-api-key>
   ```
   > ⚠️ There must be exactly **one space** between `ApiKey` and the key. Missing it will cause auth to fail.
4. Click **Connect**.
5. Expand the agent card and review the JSON — look for capabilities, auth hints, and the A2A endpoint.

![agent-card](images/image-50.png)

---

### 6.3 Chat with Your Agent via A2A

Once connected, the Inspector's **Live Chat** tab lets you send messages directly to the `potter-answers` agent. The **Debug Console** (slide-out) shows the raw JSON-RPC 2.0 messages exchanged — great for understanding what's happening under the hood.

Try the same questions from Module 5:

```
What is Quidditch, and which Hogwarts House does Hagrid warn Harry against, linking it to dark wizards?
```

```
Describe the appearance of the famous pub entrance that only witches and wizards could see.
```

**What to observe:**
- Responses are grounded in the Harry Potter chapter data via the `potter.chapter.5` tool — not generic LLM knowledge
- The Debug Console shows A2A JSON-RPC messages, making the protocol transparent
- The Spec Compliance check flags any deviations from the A2A specification

![agent-card-full-o11y](images/image-51.png)

---

### Key Takeaways

1. Elastic Agent Builder exposes an A2A endpoint for every agent out of the box — no extra setup required
2. The agent card is the contract between your agent and any A2A-compatible client — inspect it to understand capabilities and auth before wiring up integrations
3. The A2A Inspector is a fast way to validate your agent is reachable and spec-compliant before building a production client
4. The Debug Console gives you full visibility into the JSON-RPC messages, making A2A easy to reason about and debug

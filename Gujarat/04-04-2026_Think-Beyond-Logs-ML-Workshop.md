# Open Source Day Workshop — Think Beyond Logs: Machine Learning, AIOps, and AI Assistant with Elastic!
**Ahmedabad | 04th April 2026**

---

## Goal: Use raw web logs to find anomalies, explained spikes, and AI-assisted investigation all in Elastic. You'll become a Machine Learning Engineer and run jobs to process billions of logs in near-realtime.

Meet Our Workshop Volunteers!

We're excited to have an amazing lineup of volunteers from [Elastic User Group Ahmedabad](https://www.meetup.com/gujarat-elastic-fantastics/) who will guide you through this comprehensive workshop:

- Introduction to Elasticsearch – by [Someshwaran M](https://www.linkedin.com/in/somdevsupport/)
- Dive into Machine Learning in Elastic – by [Dhairya Panchal](https://www.linkedin.com/in/dhairya-panchal-800845351/) and [Achyut Hadavani](https://www.linkedin.com/in/achyut-hadavani/)
- ML On Demand with AIOps Labs – by [Vanshika Tripathi](https://www.linkedin.com/in/vanshika-tripathi-657098280/) and [Rachana Chauhan](https://www.linkedin.com/in/rachana-chauhan/)
- Elastic AI Assistant with RAG – by [Shiv Jani](https://www.linkedin.com/in/shiv-jani/) and [Harshvardhansinh Jadeja](https://www.linkedin.com/in/harshvardhansinh-jadeja-86250b348/)

## Table of Contents

1. [Before We Begin — Adding Sample Data](#0-before-we-begin-adding-the-sample-data)
2. [Section 1 — Machine Learning](#1-machine-learning)
   - [Discover Session & Dashboard](#lets-discover-our-dataset)
   - [Multi-Metric Job](#multi-metric-job)
   - [Population Job](#population-job)
3. [Section 2 — AI Ops](#2-ai-ops)
   - [Log Rate Analysis](#log-rate-analysis)
   - [Log Pattern Analysis](#log-pattern-analysis)
   - [End-to-End Analysis](#end-to-end-analysis-within-your-dashboard)
4. [Section 3 — Elastic AI Assistant](#3-elastic-ai-assistant)
   - [Setting Up LLM Connection](#setting-up-your-llm-connection)
   - [Chat With Your Data](#chat-with-your-data)


---

# Machine Learning

## Understanding Machine Learning: A Beginner's Guide

Before we dive into the hands-on workshop, let's understand some fundamental concepts about machine learning and why it's so powerful for analyzing large volumes of data.

### The Challenge: Finding Patterns in Billions of Log Lines

Imagine you're responsible for monitoring a website that generates millions of log entries every day. Each log entry contains information about:
- Who accessed your site (IP addresses)
- What pages they visited
- When they visited
- Whether the request succeeded or failed
- Response codes, error messages, and more

**The problem?** It's humanly impossible to manually review billions of log lines to identify:
- Unusual patterns that might indicate security threats
- Anomalies that could signal system problems
- Trends that could help optimize performance

This is where **machine learning** comes in. Machine learning algorithms can automatically analyze vast amounts of data, learn normal patterns, and flag anything that deviates from the norm—all in real-time.

### Supervised vs Unsupervised Learning

There are two main approaches to machine learning:

**Supervised Learning:**
- Requires labeled training data (you tell the model what's "normal" and what's "anomalous")
- Like teaching with examples: "This is a cat, this is a dog"
- Works well when you have historical examples of what you're looking for
- **Challenge:** You need to know what you're looking for in advance and have labeled examples

**Unsupervised Learning:**
- Doesn't require labeled data—it learns patterns on its own
- The model discovers what's "normal" by analyzing the data itself
- Then identifies anything that doesn't fit the normal pattern
- **Advantage:** Perfect for situations where you don't know what anomalies look like yet

### Why Elasticsearch for Machine Learning?

Elasticsearch's machine learning capabilities use **unsupervised learning**, which makes it ideal for log analysis because:

1. **No Training Required:** You don't need to manually label thousands of examples. The system learns what's normal from your actual data.

2. **Real-Time Detection:** As new data comes in, the system continuously learns and adapts, detecting anomalies as they happen.

3. **Built-In Integration:** Machine learning is natively integrated into Elasticsearch and Kibana, so you don't need separate tools or complex setups.

4. **Easy to Use:** You don't need to be a data scientist. Elastic provides intuitive wizards that guide you through creating machine learning jobs.

5. **Automatic Pattern Recognition:** The system automatically identifies:
   - Unusual spikes in traffic
   - Anomalous user behavior
   - Patterns that would take humans days or weeks to find

### What We'll Learn in This Workshop

In this workshop, you'll learn how to:
- Use Elastic's machine learning to automatically detect anomalies in web logs
- Identify suspicious activities and potential security threats
- Create visualizations that highlight unusual patterns
- Set up real-time monitoring that alerts you to problems

---

## #0 Before We Begin Adding the Sample Data

We'll be using Elastic Serverless (Observability Solution) for this session. It runs the latest Elastic version.

> **Important:** Go to [cloud.elastic.co](https://cloud.elastic.co) to create a new **Observability** project (not Elasticsearch) from your Elastic Cloud profile to get access to Anomaly Detection and all ML features.

![Create new project](images/image1.png)

![Select Observability](images/image2.png)

![Project setup](images/image3.png)

![Project ready](images/image4.png)

To load the sample data, navigate to **Integrations**

![Navigate to Integrations](images/image5.png)

Search for **Sample Data** and click the **Sample Data** block. From there, add all three available datasets: **Web Logs**, **Flight Data**, and **eCommerce Data**

![Search Sample Data](images/image6.png)

![Add all three datasets](images/image7.png)

---

## #1 Machine Learning

### Let's Discover Our Dataset

- Go to **Discover** from the left sidebar

![Discover menu](images/image8.png)

- Once in Discover, select the **Kibana Sample Data Logs** dataview from the drop-down menu

![Select dataview](images/image9.png)

- In the time picker at the top right, click on the calendar icon to select **The last 30 days**

![Time picker - last 30 days](images/image10.png)

- Build the Discover Session. In the field search bar, add the following fields in this order:
  - `geo.src`, `geo.dest`
  - `machine.os`, `clientip`, `bytes`
  - `host`, `request`, `response`
  - `referer`, `extension`

![Adding fields to Discover](images/image11.png)

You should see the following Discover session:

![Discover session result](images/image12.png)

- Save your Discover Session by clicking the top right **Save** button. On the popup:
  - Name it `discover_session_kdsl`
  - Toggle on **"Store time with Discover Session"**
  - Click **Save**

![Save Discover session](images/image13.png)

- In the **Breakdown** field, look for "host" and click on `host.keyword`

![Breakdown field - host.keyword](images/image14.png)

- Save again to overwrite. You should now see:

![Final Discover session](images/image15.png)

---

### Let's Build a Dashboard

- Go to **Dashboards** from the sidebar → click **Create dashboard**

- You will see two options — click on **Add from library**

![Add from library option](images/image16.png)

- On the popup:
  - In the search bar, look for `discover_session_kdsl`
  - Click once on the result to add it to the dashboard
  - Close the popup

![Add discover session to dashboard](images/image17.png)

Verify the last 30 days data is showing:

![Verify 30 days data](images/image18.png)

- Save your dashboard as `dashboard_kdsl`. Don't forget to toggle on **"Store time with dashboard"** before saving.

![Save dashboard](images/image19.png)

- Click the **Save** button at the top right. After that, click **Exit** to switch to view mode.

![Switch to view mode](images/image20.png)

- Go to **Discover** via the sidebar → click the **folder icon** at the top right

![Folder icon in Discover](images/image21.png)

- On the flyout, find `discover_session_kdsl` and double-click it to load

![Load saved session](images/image22.png)

- Once loaded, click the **Lens** icon at the far right of the visualization graph

![Lens icon](images/image23.png)

![Lens editor](images/image24.png)

- Click the **Save** button on the top right. On the popup:
  - Enter a title for your Lens visualization
  - Add to existing dashboard `dashboard_kdsl`
  - Click **Save and go to Dashboard**

![Save to dashboard](images/image25.png)

Don't forget to save from the dashboard after:

![Save dashboard again](images/image26.png)

---

### Multi-Metric Job

In this challenge, we will analyze unusual high counts of events rate (web log records) and find the possible causes (aka "influencers") of those anomalies.

- Go to **Machine Learning** from the left sidebar
- Click **Anomaly Detection > Jobs**
- Click **Create anomaly detection job**

![Machine Learning Jobs](images/image27.png)

![Create job button](images/image28.png)

- Select the `discover_session_kdsl` dataview or the **Kibana Sample Data Logs** index

![Select data view](images/image29.png)

- On the wizard screen, click **Multi-metric**

![Select Multi-metric](images/image30.png)

#### Time Range

- Click **Use full data**
- For the ending date, **select a date 3 months ahead of today** (e.g. if today is January, select April)
- Click **Next**

![Time range configuration](images/image31.png)

#### Choose Fields

- **Metric:** `High count (Event rate)`
- **Split field:** `response.keyword`
- **Bucket Span:** keep `15m`
- **Influencers:** `response.keyword`, `clientip`, `request.keyword`
- Click **Next**

![Choose fields](images/image32.png)

#### Job Details

- **Job ID:** `ml_kdsl_unusual_high_event_count_rate`
- Unfold **Advanced** → toggle on **"Enable model plot"** and **"Enable model change annotation"**
- Keep memory at **11MB**
- Click **Next**

![Job details](images/image33.png)

![Advanced settings](images/image34.png)

- The job should pass all validations. Click **Next**

![Validation passed](images/image35.png)

- Click **Create job** → wait for it to finish
- Click **Start job running in real time**
- Click **View results** to jump into the Anomaly Explorer

![Job running](images/image36.png)

![View results](images/image37.png)

#### Analyzing Results in the Anomaly Explorer

![Anomaly Explorer overview](images/image38.png)

![Anomaly Explorer detail](images/image39.png)

We can notice:

- An unusual high count of **503 errors** (server internal error) — **16x above normal**
- From clientips: `214.190.64.93`, `65.45.138.4`, `72.226.44.128`
- While trying to download `filebeat-6.3.2-linux-x86.tar.gz` and `elasticsearch-6.3.2.deb` packages

**Question #1: What other anomalies do you see?**

**Answer:**
- Massive peak of requests to non-existing pages (404) from clientip `30.156.16.164`
- Repeated/automated download of elastic apm debian package (200)
- Attempts to access `.tar` and `.deb` files resulted in (503) errors due to service overload

- Click on the **503 red square** to investigate deeper:

![503 anomaly detail](images/image40.png)

**Question #2: What do you notice?**

**Answer:**
- An unusual request targeting a "profile" path `/people/type=astronauts/name=valery-tokarev/` answered with 503
- Looks like targeted scraping or bot testing

#### Add Swimlane to Dashboard

- Click the **3 dots** at the left of the swimlane → **Add to dashboard** → **View by response.keyword**

![Add swimlane - 3 dots menu](images/image41.png)

![Add swimlane popup](images/image42.png)

- Fill the popup and click **Save and go to Dashboard**

![Save swimlane to dashboard](images/image43.png)

> **Warning:** After adding the swimlane, don't forget to **save your dashboard** and switch back to view mode.

---

### Population Job

In this challenge, we will analyze the behavior of `clientip` entities at a given time and find outliers — entities that do not behave the same as others.

- Go to **Machine Learning > Anomaly Detection > Jobs** → **Create job**
- Select `discover_session_kdsl` or **Kibana Sample Data Logs**
- Select **Population** job type

#### Time Range

- Click **Use full data**
- Set end date **3 months ahead**
- Click **Next**

#### Choose Fields

- **Population field:** `clientip`
- **Add metric:** `Distinct count(request.keyword)`
- **Influencers:** `clientip`, `response.keyword`
- Leave Bucket span as is
- Click **Next**

#### Job Details

- **Job ID:** `ml_kdsl_unusual_clientip_behavior`
- Unfold **Advanced** → toggle on **"Enable model change annotations"**
- Click **Next** → pass Validation → **Create job**
- Wait for completion → **Start job running in real time** → **View results**
- Add the swimlane (view by `clientip`) to `dashboard_kdsl`

> **Warning:** After adding the swimlane, don't forget to save your dashboard and switch back to view mode.

Your dashboard should now look like this:

![Dashboard with swimlanes](images/image44.png)

- Go back to **Machine Learning > Anomaly Detection > Jobs** and click the anomaly explorer icon for the population job

![Anomaly explorer icon](images/image45.png)

You should see this Anomaly Explorer:

![Population Anomaly Explorer](images/image46.png)

**Question #3: What is the faulty clientip?**

**Answer:** `30.156.16.164` has a huge and unusually large number of 404 responses compared to all other IPs.

**Question #4: Would you explain why?**

**Hints:**
- Click the **wheel icon** on the anomaly line at the far right
- Go to Discover from the popup menu to see the raw log records
- Investigate the `request` and `url` fields

**Answer:** `30.156.16.164` is a bot crawling dynamically generated URLs on `https://elastic-elastic-elastic.org` — a domain that does not exist.

![Bot investigation](images/image47.png)

#### Conclusion

Elastic Machine Learning offers a powerful yet accessible approach to anomaly detection:

- All data is processed within the Elastic platform — no external AI environments needed, ensuring data privacy
- It's a no-code solution with intuitive graphical wizards for SecOps, ITOps, and DevOps teams
- Simple, flexible, and natively integrated — easy to adopt and adapt

**Elastic ML: Smart. Secure. Seamless.**

---

## #2 AI Ops

We've seen how powerful Elastic's built-in ML is.

> 👉 **But what if I just need a one-off investigation?**
> 👉 **Do I have to create ML jobs for that?** Not at all!

AIOps Labs tools give you instant on-demand investigation with no setup required.

### Log Rate Analysis

- Go to **Machine Learning > AIOps Labs > Log Rate Analysis**
- Select `discover_session_kdsl` or **Kibana Sample Data Logs**
- Click **Use full data** → set end date 3 months ahead
- You will notice an **orange peak** of logs:

![Orange peak in Log Rate Analysis](images/image48.png)

![Log rate chart](images/image49.png)

![Analysis starting](images/image50.png)

- Click on the orange peak — analysis starts immediately. A deviation is detected:

![Deviation detected](images/image51.png)

**Question #1: What is at the origin of this spike?**

**Answer:** clientip `30.156.16.164` triggered the peak of logs.

- Add the tool to `dashboard_kdsl` via the right menu:

![Add to dashboard menu](images/image52.png)

![Add panel popup](images/image53.png)

> **Note:** No need to toggle on "Apply time range"
>
> **Warning:** After adding, save your dashboard and switch back to view mode.

---

### Log Pattern Analysis

- Go to **Machine Learning > AIOps Labs > Log Pattern Analysis**
- Select `discover_session_kdsl` or **Kibana Sample Data Logs**
- Click **Use full data** → set end date 3 months ahead
- **Category field:** `message` (the original raw log line before parsing)
- Click **Run pattern analysis**

![Log Pattern Analysis](images/image54.png)

- Add the "Log Pattern Analysis" panel to your dashboard

> **Note:** No need to toggle on "Apply time range"
>
> **Warning:** After adding, save your dashboard and switch back to view mode.

---

### End-to-End Analysis Within Your Dashboard

- Open `dashboard_kdsl` → set time picker: **last 30 days to 3 months ahead**

![Dashboard time range](images/image55.png)

- On the **unusual clientip swimlane**, right-click the red square for `30.156.16.164` → click **"Filter for value"**

![Filter for value](images/image56.png)

**Question #2: What more can you conclude about `30.156.16.164`?**

**Hints:** Look at the filtered "Log Pattern Analysis" visualization.

**Answer:** `30.156.16.164` is not just a crawling bot. It also performs a cyber attack trying to bypass password protection or perform a CGI exploit.

#### Conclusion

AIOps gives you instant answers to:

> 👉 Why am I suddenly seeing all these logs?
> 👉 Where are they coming from?

You also get a clear overview of log content without writing any complex regular expressions — Elasticsearch handles that automatically as a full-text search engine by design.

---

## #3 Elastic AI Assistant

ML and AIOps Labs are powerful for detecting anomalies and performing root cause analysis.

> 👉 **But what if you're new to Elastic?**
> 👉 **Can you still explore your data and get query suggestions?**

That's exactly the promise of the **Elastic AI Assistant** — an agentic RAG application that connects to an LLM of your choice (OpenAI, Azure, Google Vertex AI, AWS Bedrock, or local models like Ollama/LM Studio).

---

### Setting Up Your LLM Connection

#### Step 0: You can either setup using Elastic Serverless or choose your own or local models.

#### Setting up via Elastic Serverless (Easy-to-Setup)

#### Step 1: 

- Go to **Observability** Home Page > Select **AI Assistant** on the top right hand corner 

![serverless-ai-assistant-01](images/serverless-ai-assistant-01.png)

#### Step 2: 

Now, click here -> go to [Chat With Your Data](#chat-with-your-data)

#### Setting up via Own or Local Models

#### Step 1: Navigate to Connectors

- Go to **Stack Management > Connectors**

![Stack Management Connectors](images/image57.png)

- Click **Create Connector** and select **OpenAI** (or your preferred provider)

![Create Connector](images/image58.png)

---

#### Step 2: Configure Your Connector

**Option A: Cloud-based LLM Providers (OpenAI, Azure, AWS Bedrock, Google Vertex)**

1. Select your provider from the dropdown:
   - **OpenAI** — GPT-4, GPT-3.5, etc.
   - **Azure OpenAI** — Azure-hosted models
   - **Amazon Bedrock** — Claude, Llama, etc.
   - **Google Vertex** — Gemini models

2. Fill in the required fields:
   - **URL:** auto-filled by provider
   - **Default model:** e.g. `gpt-4` or `claude-3-sonnet-20240229`
   - **API Key:** from your provider

3. Click **Save** and test using the **Test Tab**

![Test tab for cloud connector](images/image59.png)

---

**Option B: Local LLM with Ollama**

Prerequisites:
- Install [Ollama](https://ollama.ai/) on your laptop
- Download a model: `ollama pull llama2`

**For Elastic Cloud — expose local server:**

```bash
# Method 1: ngrok (recommended for testing)
# Install ngrok: https://ngrok.com/
ngrok http 11434
# Copy the HTTPS URL (e.g. https://abc123.ngrok.io)
```

Configure the connector:
- Select **OpenAI** → **Other (OpenAI Compatible Service)**
- **URL:** `https://your-ngrok-url.ngrok.io/v1/chat/completions`
- **Default model:** `llama2`
- **API Key:** leave empty

**For On-Prem Elastic — direct connection:**
- **URL:** `http://localhost:11434/v1/chat/completions`
- **Default model:** `llama2`
- **API Key:** leave empty

---

**Option C: Local LLM with LM Studio**

Prerequisites:
- Install [LM Studio](https://lmstudio.ai/)
- Load a model (model name must include `instruct`)

**For Elastic Cloud:**

```bash
# Start LM Studio server
lms server start   # default port: 1234

# Expose with ngrok
ngrok http 1234
```

Configure the connector:
- Select **OpenAI** → **Other (OpenAI Compatible Service)**
- **URL:** `https://your-ngrok-url.ngrok.io/v1/chat/completions`
- **Default model:** `local-model`

**For On-Prem Elastic:**
- **URL:** `http://localhost:1234/v1/chat/completions`
- **Default model:** `local-model`
- **API Key:** leave empty

---

#### Step 3: Test Your Connection

- Go to the **Test Tab** → click **Run**

![Test connector](images/image59.png)

- If the test passes — you're ready!
- If it fails, check:
  - API key is correct (cloud providers)
  - Local server is running (local LLMs)
  - URL is publicly accessible (cloud Elastic + local LLM)
  - Firewall settings allow the connection

![Connector configured](images/image60.png)

> **Security Note:** When exposing local servers publicly, always use proper authentication (API keys/tokens) and HTTPS with SSL/TLS certificates for production.

---

### Chat With Your Data

Ask exactly the following questions to the Elastic AI Assistant. Wait for each answer to fully complete before sending the next question.

#### In Tamil

**Question 1**
```
எனது Kibana sample data logs-ல், response code 400-க்கு மேல் அல்லது சமமாக உள்ள rejected requests-களுக்கு காரணமான clientip addresses-ஐ எனக்கு சொல்ல முடியுமா? response.keyword field-ஐ பயன்படுத்தவும்.
```

**Question 2**
```
இந்த எண்களிலிருந்து ஒரு vertical bar graph-ஐ உருவாக்க முடியுமா?
```

#### In Hindi

**Question 3**
```
clientip 30.156.16.164 से उत्पन्न logs से, इस IP address का भौगोलिक मूल स्थान खोजें।
```

**Question 4**
```
clientip 30.156.16.164 कौन से URLs तक पहुंचने की कोशिश कर रहा है? आप ESQL भाषा के CATEGORIZE directive का उपयोग कर सकते हैं।
```

#### In English

**Question 1**
```
In my Kibana sample data logs, can you tell me which clientip addresses are responsible for the highest number of rejected requests — that is, requests with a response.keyword field greater than or equal to 400?
```

**Question 2**
```
Can you create a vertical bar graph out of these figures, please?
```

**Question 3**
```
From the logs originating from clientip 30.156.16.164, can you find the geographical origin of this IP address?
```

**Question 4**
```
What is the pattern of URLs that clientip 30.156.16.164 is trying to access? You can use the CATEGORIZE directive from the ESQL language.
```

> **Warning:** Wait for the Elastic AI Assistant to fully complete each answer before moving to the next question.

#### Conclusion

| Benefit | Description |
|---|---|
| 👉 No need to know all field names | The assistant understands your intent and handles field discovery for you. |
| 👉 Automatically generates ES\|QL queries | Describe what you need and it writes the query for you. |
| 👉 Generates reusable visualizations | Quickly create graphs you can use in dashboards or refine further. |
| 👉 Explains ongoing incidents | Provides context and insights without manual investigation. |

---

In this session, we created `Elastic Serverless Observability` project, loaded the sample logs, and built a Discover session and `dashboard_kdsl` as our single source of truth. With Elastic ML we ran multi-metric and population anomaly jobs and surfaced real issues—especially unusual 503 traffic and the outlier 30.156.16.164, which we traced to bot-like crawling and richer abuse patterns using AIOps Labs on the same dashboard. 

We finished with the Elastic AI Assistant, showing natural-language questions (including Tamil and Hindi) that produce ES|QL and charts—so discovery, automated detection, on-demand analysis, and conversational exploration all stay inside Elastic.

---

## References and Additional Resources

For more information about Elastic Machine Learning and related topics, please refer to the following resources:

- [What is Machine Learning?](https://www.elastic.co/what-is/machine-learning) - Introduction to machine learning concepts
- [Elasticsearch Machine Learning](https://www.elastic.co/what-is/elasticsearch-machine-learning) - Overview of machine learning in Elasticsearch
- [Machine Learning Guide](https://www.elastic.co/guide/en/machine-learning/current/index.html) - Complete documentation for Elastic Machine Learning
- [Elastic Machine Learning Models](https://www.elastic.co/search-labs/blog/elastic-machine-learning-models) - Blog post about ML models in Elastic
- [Enterprise Search Machine Learning](https://www.elastic.co/enterprise-search/machine-learning) - Machine learning for enterprise search
- [AIOps](https://www.elastic.co/observability/aiops) - AIOps capabilities in Elastic Observability
- [Accelerate Security Investigations with Machine Learning](https://www.elastic.co/virtual-events/accelerate-security-investigations-with-machine-learning-and-interactive-root-cause-analysis-in-elastic) - Virtual event on ML for security investigations

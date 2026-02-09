# Elastic Workshop: Machine Learning, AI Ops, and ESQL

## 🎤 Meet Our Speakers

We're excited to have an amazing lineup of speakers who will guide you through this comprehensive workshop:

- 🔎 **Introduction to Elasticsearch** – by [Someshwaran M](https://www.linkedin.com/in/somdevsupport/)
- 🤖 **Dive into Machine Learning in Elastic** – by [Kishore Kumar](https://www.linkedin.com/in/kishore-loga/)
- ⚙️ **ML On Demand with AIOps Labs** – by [Aravind Sathyajith](https://www.linkedin.com/in/sathyajith98/)
- 🧠 **Elastic AI Assistant with RAG** – by [Aakash](https://www.linkedin.com/in/aakash-dhakshnamoorthy-6909991ab/) & [Harish](https://www.linkedin.com/in/harish-h2h/)
- 📊 **Have Fun with ES|QL** – by [Shane Cardoz](https://www.linkedin.com/in/shane-cardoz-m/)
- 🚨 **From Discover to Alerting** – by [Navin Balaji](https://www.linkedin.com/in/navinbalaji/)

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

### LLM Setup Requirements

For the **Elastic AI Assistant** section (#3), you'll need to configure a connection to a Large Language Model (LLM). You have several options:

**Option 1: Cloud-based LLM Providers** (Easiest - No local setup required)
- **OpenAI** - Requires an API key from [OpenAI](https://platform.openai.com/)
- **AWS Bedrock** - Requires AWS account with Bedrock access
- **Azure OpenAI** - Requires Azure subscription
- **Google Vertex AI** - Requires Google Cloud account

**Option 2: Local LLM** (Free - Requires laptop with sufficient resources)
- **Ollama** - Easy to set up, runs models locally
- **LM Studio** - User-friendly GUI for local models
- Requires exposing the local server (for cloud Elastic) or direct connection (for on-prem Elastic)

> **Note:** If you're using a local LLM with Elastic Cloud, you'll need to expose your local server publicly using a reverse proxy or tunneling service (like ngrok). For on-prem Elastic deployments, you can connect directly to localhost.

Once you have your Elastic Cloud account set up and have chosen your LLM option, you're ready to begin!

---

## Workshop Timeline & Navigation

This workshop is divided into 5 main sections. Use the links below to quickly navigate to any section:

### 📚 Workshop Overview

1. **[Machine Learning](#machine-learning)** - Introduction to ML concepts and anomaly detection
   - [Understanding Machine Learning: A Beginner's Guide](#understanding-machine-learning-a-beginners-guide)
   - [Introduction: Machine Learning With Elastic](#1-introduction-machine-learning-with-elastic)
   - [Multi-Metric Job](#multi-metric-job)
   - [Population Job](#population-job)

2. **[AI Ops](#2-ai-ops)** - On-demand investigation tools
   - [Log Rate Analysis](#log-rate-analysis)
   - [Log Pattern Analysis](#log-pattern-analysis)
   - [End-to-End Analysis Within Your Dashboard](#end-to-end-analysis-within-your-dashboard)

3. **[Elastic AI Assistant](#3-elastic-ai-assistant)** - Natural language queries with AI
   - [A Pre-configured Connection to an LLM has been setup](#a-pre-configured-connection-to-an-llm-has-been-setup-check-its-working-fine)
   - [Chat With Your Data](#chat-with-your-data) (Tamil, Hindi, English)

4. **[ESQL: {ES\|QL}](#4-esql-esql)** - Elasticsearch Query Language
   - [Your First Query](#your-first-query)
   - [Query Filtering](#query-filtering)
   - [Query Aggregations](#query-aggregations)
   - [Results Presentation](#results-presentation)

5. **[From Discover to Alerting](#5-from-discover-to-alerting)** - Setting up alerts
   - [Cancelled Flights](#cancelled-flights)
   - [Configure the Alert](#configure-the-alert)

### 📖 Additional Resources

- [References and Additional Resources](#references-and-additional-resources) - Links to documentation and learning materials

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

**Ready to get started?** Let's begin by exploring how machine learning can help monitor website operations!

---

# #1 Introduction: Machine Learning With Elastic

When operating a website, one must ensure:

- its performance (latency, bandwidth),
- its proper application behavior (CPU, RAM metrics, system errors),
- that web pages are served correctly (404 errors, 500 application errors, etc.),
- and that the site is protected against malicious users or bots (URL scanning, DDoS attacks, etc.).

We will now see how Elastic's machine learning capabilities can help with the last two points through two jobs:

- The 1st **Multi-Metric** job will analyze the number of access logs by response code over time and provides insights into potential causes when anomalies occur.
- The 2nd **Population** job will compare client behavior at a given time to detect unusual or suspicious activity.

## Let's discover our dataset:

- Go to the Discover by clicking on the hamburger menu at the top left, then on "Discover"

![Discover Menu](images/image1.png)

- Once in the Discover, select the **Kibana Sample Data Logs** dataview in the drop-down menu

![Select Dataview](images/image2.png)

- In the timepicker, at the top right, click on the calendar icon to select **The last 30 days**

![Time Picker](images/image3.png)

- Let's now build our Discover Session. In the field search bar, search for `geo..` The fields menu will list now only matching fields. Click on the circle + sign to add the "geo.src" field in the displayed columns of the main view. Do this for the following fields in this order:
  - `geo.src`, `geo.dest`
  - `machine.os`, `clientip`, `bytes`
  - `host`, `request`, `response`
  - `referer`, `extension`

![Build Discover Session](images/image4.png)

You should see the following discover session

![Discover Session](images/image5.png)

- Save your Discover Session by clicking on the top right **Save** button. On the appearing popup screen:
  - name it `discover_session_kdsl`,
  - toggle on the "Store time with Discover Session" switch.
  - And then click on **Save**

![Save Discover Session](images/image6.png)

- In the "Breakdown" field, look for "host" and click on "host.keyword" as follow:

![Breakdown Field](images/image7.png)

- Save again your Discover Session to overwrite as you did in step 5. You should end seeing the same Discover Session as below

![Saved Discover Session](images/image8.png)

## Let's build a dashboard:

- Go to the hamburger menu and then click on "Dashboards"

![Dashboards Menu](images/image9.png)

- Click on **Create dashboard**

![Create Dashboard](images/image10.png)

- Click on **Add from library**. On the appearing popup:
  - In the search bar, look for the previously saved discover session (`discover_session_kdsl`)
  - Click once on the corresponding result link. The discover session will be automatically added in the dashboard
  - Close the popup

![Add from Library](images/image11.png)

- Extend the Discover Session on the whole width of the dashboard and click on the top right **Save** button

![Extend Discover Session](images/image12.png)

- Save your dashboard as `dashboard_kdsl`. Don't forget to toggle on "Store time with dashboard" before saving.

![Save Dashboard](images/image13.png)

- "Switch to view mode"

![Switch to View Mode](images/image14.png)

- Go to the Discover via the hamburger menu.
- Click on the top right "folder" icon.
- On the appearing flyout, look for your saved discover session `discover_session_kdsl`
- Double-click on it to load it

![Load Discover Session](images/image15.png)

- Once your discover session is loaded, click on the "Lens" icon at the far right of the visualization graph.

![Lens Icon](images/image16.png)

- You are taken to Lens to edit the visualization. Click to display 5 hosts instead of 3

![Lens Visualization](images/image17.png)

- Click on the top right **Save** button. On the appearing popup:
  - Enter a title for your lens visualization
  - Add to the existing dashboard `dashboard_kdsl`
  - Click on **Save and go to Dashboard** button

![Save and Go to Dashboard](images/image18.png)

- Save your dashboard as in step 5 and "Switch to view mode". You should see the following dashboard

![Final Dashboard](images/image19.png)

## Multi-Metric Job:

In this challenge, we are going to analyze unusual high counts of events rate (web log records) and find the possible causes (aka "influencers") of those anomalies.

- Go to the Hamburger Menu and then click on the **Machine Learning** link under the "Analytics" section.
- Once in the Machine Learning menu, click on the **Jobs** link under the "Anomaly Detection" section, ie **Machine Learning > Anomaly Detection > Jobs**. And then click on the **Create anomaly detection job** button

![Machine Learning Jobs](images/image20.png)

- Select the `discover_session_kdsl` dataview or the **Kibana Sample Data Logs** index

![Select Dataview](images/image21.png)

- On the general wizard screen, click on "Multi-metric" to enter the dedicated wizard to multi-metric jobs

![Multi-metric Wizard](images/image22.png)

### Time range

Select the timerange of data to analyze. Do in this order:

- Click 1st on the **Use full data** button.
- For the ending date of the time range period, **select a date ahead of now by 3 months**. For instance if we are in July, select October
- Then click on the **> Next** button to move to the next step

![Time Range Configuration](images/image23.png)

### Choose fields

Do the following:

- Regarding the metric to monitor for anomaly detection, select **High count(Event rate)**
- **Split field**: enter `response.keyword`
- **Bucket Span**: keep `15m`
- **Influencers**: in addition of `response.keyword`, add `clientip` and `request.keyword`
- Then click on the **> Next** button to move to the next step

![Choose Fields](images/image24.png)

### Job details

Fill as follow:

- **Job ID**: `ml_kdsl_unusual_high_event_count_rate`
- Unfold the "Advanced" subsection and toggle on "Enable model plot" and "Enable model change annotation"
- Keep to memory requirement to **11MB**
- Click on **> Next** to move to the next step

![Job Details](images/image25.png)

- The job should pass all validations. Click on the **> Next** button.

![Validation](images/image26.png)

- Start the machine learning job by clicking on the **Create job** button. Your job should start and progress along your dataset, raising a 1st set of few anomalies.
- Once finished, the **Start job running in real time** button will appear. Click on it
- then click on the **View results** button to jump into the "Anomaly Explorer"

![View Results](images/image27.png)

### Let's analyze the results within the "Anomaly Explorer"

![Anomaly Explorer](images/image28.png)

We can notice:
- an unusual high count of 503 errors (server internal error) - **16x times than unusual**
- regarding the following clientip: `214.190.64.93`, `65.45.138.4`, `72.226.44.128`
- while they were trying to download `filebeat-6.3.2-linux-x86.tar.gz` and `elasticsearch-6.3.2.deb` packages

<details>
<summary><strong>Question #1: What other anomalies do you see?</strong></summary>

**Answer to Question #1**:

- massive peak of requests to inexisting page (404) from the same clientip `30.156.16.164`
- repeated or automated download of elastic apm debian package (200)
- Attempts to access .tar and .deb files resulted in (503) errors, likely due to service overload or temporary unavailability.

</details>

- Click on the 503 red square to know deeper about the error. You should see the following refined "Anomaly explorer"

![Refined Anomaly Explorer](images/image29.png)

<details>
<summary><strong>Question #2: What do you notice?</strong></summary>

**Answer to Question #2**:

- an unusual request targeting a "profile" under the path `/people/type=astronauts/name=valery-tokarev/` answered with code (503)
- Looks like a targeted scraping or testing URL by a bot.

</details>

- Add the anomaly swimlane to the `dashboard_kdsl` dashboard
  - Click on the 3 dots at the left of the swimlane
  - Click on "Add to dashboard"
  - Click on "View by response.keyword"

![View by Response](images/image30.png)

  - On the popup, fill it as follow and click on **Save and go to Dashboard**

![Add to Dashboard](images/image31.png)

> **Warning**: Once the anomaly swimlane is added, don't forget to save your dashboard and switch back into view mode.

## Population Job:

In this challenge, we are going to analyze the behavior of clientip entities at a given time and find outliers, ie entities that does not behave same as others.

- Go to **Machine Learning > Anomaly Detection > Jobs**
- Click on the **Create job** button on your right
- Select your `discover_session_kdsl` dataview or the **Kibana Sample Data Logs** index
- On the wizard, select the "Population" job

### Time Range

- Click on **Use full data** button
- In the time picker, for the ending date of the time range period, **select a date ahead of now by 3 months**. For instance if we are in July, select October
- Click on the **>Next** button

### Choose fields

- **Population field**: `clientip`
- **Add metric**: `Distinct count(request.keyword)`
- **Influencers**: `clientip`, `response.keyword`
- Leave the **Bucket span** as is.
- Click on the **>Next** button

### Job details

- **Job ID**: `ml_kdsl_unusual_clientip_behavior`
- Unfold **Advanced** and toggle on "Enable model change annotations"
- Click on the **>Next** button

- Pass the **Validation** step
- Click on the **Create job** button
- Wait for the job to complete and click on **Start job running in real time**
- Click on **View results**
- Once taken to the "Anomaly Explorer", add the swimlane (view by clientip) to the `dashboard_kdsl` dashboard.

> **Warning**: Once the anomaly swimlane is added, don't forget to save your dashboard and switch back into view mode.

- Your dashboard should look like below:

![Dashboard with Swimlane](images/image32.png)

- Go back to **Machine Learning > Anomaly Detection > Jobs** and click on the anomaly explorer icon of the population job we have just set up and started.

![Anomaly Explorer Icon](images/image33.png)

- You should see the following Anomaly Explorer:

![Population Anomaly Explorer](images/image34.png)

<details>
<summary><strong>Question #3: What is the faulty clientip?</strong></summary>

**Answer to Question #3**: Compared to the others clientip, `30.156.16.164` seems to meet a huge and large number of 404 answers

</details>

<details>
<summary><strong>Question #4: Would you explain why?</strong></summary>

**Hints for Question #4**:

- Click on the wheel icon on the anomaly line you identified at the far right
- On the popup menu, go to the Discover. You will get to the log records at the origin of the anomaly.
- Investigate the `request` and `url` fields

**Solutions for Question #4**: `30.156.16.164` seems to be a bot crawling dynamically generated urls in `https://elastic-elastic-elastic.org` which does not exist

</details>

![Question 4 Solution](images/image35.png)

## Conclusion

Elastic Machine Learning offers a powerful yet accessible approach to anomaly detection and operational intelligence:

- All data is processed within the Elastic platform, with no need to export to external AI environments — ensuring data privacy and compliance.
- It's a no-code solution, designed with intuitive graphical wizards to empower SecOps, ITOps, and DevOps teams to take action quickly.
- Its simplicity, flexibility, and native integration make it easy to adopt and adapt to evolving operational needs.

**Elastic ML: Smart. Secure. Seamless.**

Success!

---

# #2 AI Ops:

We've seen how easy and straightforward it is to leverage Elastic's built-in machine learning capabilities.

👉 **But what if I just need to perform a one-off investigation?**  
👉 **Do I have to create dedicated machine learning jobs for that?** Not at all!

Once again, our goal is to simplify on-demand investigations — this time using the tools provided by AIOps Labs.

In the following example, we'll show how to quickly analyze:

- A sudden spike in the number of logs being ingested
- The patterns and structure of the logs we're receiving

## Log Rate Analysis

- Go to **Machine Learning > AIOps Labs > Log Rate Analysis**
- Select the `discover_session_kdsl` dataview or the **Kibana Sample Data Logs** index
- Click on **Use full data** button
- In the time picker, for the ending date of the time range period, **Make sure you select a date ahead of now by 3 months**. For instance if we are in July, select October. Should already be ok
- You can notice an orange peak of logs as illustrate below

![Orange Peak of Logs](images/image36.png)

- Click on the orange peak. A log rate analysis is immediately started. A deviation is detected as illustrated below

![Log Rate Analysis](images/image37.png)

<details>
<summary><strong>Question #1: What is at the origin of this detected span?</strong></summary>

**Answer to Question #1**:

- Again, clientip `30.156.16.164` has triggered a peak of logs

</details>

- Add the tool to your `dashboard_kdsl` dashboard by clicking on the right menu as follow

![Add Log Rate Analysis](images/image38.png)

![Add Log Rate Analysis Menu](images/image39.png)

> **Note**: No need to toggle on "Apply time range"

> **Warning**: Once the log rate analysis panel is added, don't forget to save your dashboard and switch back into view mode.

## Log Pattern Analysis

- Go to **Machine Learning > AIOps Labs > Log Pattern Analysis**
- Select the `discover_session_kdsl` dataview or the **Kibana Sample Data Logs** index
- Click on the **Use full data** button
- In the time picker, for the ending date of the time range period, **Make sure you select a date ahead of now by 3 months**. For instance if we are in July, select October. Should already be ok.
- Make sure the **Category field** is `message`. `message` is the original log line as it was injected into Elastic before parsing. Click on the **Run pattern analysis** button.

![Log Pattern Analysis](images/image40.png)
- Add the "Log Pattern Analysis" tool to your dashboard as usual

> **Note**: No need to toggle on "Apply time range"

> **Warning**: Once the log pattern analysis panel is added, don't forget to save your dashboard and switch back into view mode.

## End-to-End Analysis Within Your Dashboard

- Go to your `dashboard_kdsl` dashboard and tune your time picker to go from **last 30 days to 3 months ahead**

![Dashboard Time Picker](images/image41.png)

- On the "unusual clientip swimlane", right click as follow on the red square corresponding to the `30.156.16.164` clientip and click on "Filter for value" as follow:

![Filter for Value](images/image42.png)

<details>
<summary><strong>Question #2: What can you conclude more as to the behavior of `30.156.16.164` clientip?</strong></summary>

**Hints for Question #2**:

- Look at the filtered "Log Pattern Analysis" visualization

**Solution for Question #2**:

- `30.156.16.164` is not a simple crawling bot. It also performs a cyber attack trying to bypass the password protection of Elastic's website or perform a cgi exploit to go through.

</details>

## Conclusion

This allows you to get quick answers in just a few clicks to questions that always matter, such as:

👉 Why am I suddenly seeing all these logs?  
👉 Where are they coming from?

You also get a clear overview of the log content, without having to write any complex regular expressions. Elasticsearch takes care of that automatically — after all, it's a full-text search engine by design.

This makes it easy to spot the most frequently accessed URLs… …but also to uncover weaker signals that might otherwise go unnoticed.

---

# #3 Elastic AI Assistant:

Machine Learning and Tools from AIOps Labs are powerful ways to detect anomalies, perform root cause analysis and trace down to the corresponding involved logs.

👉 **But what if you're new to Elastic?**  
👉 **Can you still explore your data, get guidance, or receive query suggestions to do so?**

That's exactly the promise of the Elastic AI Assistant.

The Elastic AI Assistant is an agentic RAG application that interacts with a Generative IA of your choice, that can be either self-hosted (Mistral, ...etc) or deployed on a public provider such as Azure OpenAI, OpenAI, Google Vertex AI (Gemini models) or Anthropic via AWS Bedrock (Claude Sonnet, Opus ..etc)

Your cluster comes with an Out-Of-The-Box preconfigured LLM to make this challenge easy. We just need to make sure it works or otherwise provide the right configuration.

## Setting Up Your LLM Connection

Before using the Elastic AI Assistant, you need to configure a connector to your chosen LLM. Follow the instructions below based on your setup.

### Step 1: Navigate to Connectors

- Go To **Stack Management > Connectors** as follow

![Stack Management Connectors](images/image43.png)

![Stack Management Connectors View](images/image44.png)

- Click on **Create Connector** and select **OpenAI** (or your preferred provider)

![OpenAI Connector](images/image45.png)

### Step 2: Configure Your Connector

Choose one of the following options based on your LLM provider:

#### Option A: Cloud-based LLM Providers (OpenAI, Azure, AWS Bedrock, Google Vertex)

1. Select your provider from the dropdown:
   - **OpenAI** - For OpenAI models (GPT-4, GPT-3.5, etc.)
   - **Azure OpenAI** - For Azure-hosted OpenAI models
   - **Amazon Bedrock** - For AWS Bedrock models (Claude, Llama, etc.)
   - **Google Vertex** - For Google's Gemini models

2. Fill in the required fields:
   - **URL**: Use the provider's endpoint (usually auto-filled)
   - **Default model**: Enter your model name (e.g., `gpt-4`, `claude-3-sonnet-20240229`)
   - **API Key**: Enter your API key from the provider

3. Click **Save** and test the connection using the **Test Tab**

![Test Tab](images/image46.png)

#### Option B: Local LLM with Ollama

**Prerequisites:**
- Install [Ollama](https://ollama.ai/) on your laptop
- Download a model: `ollama pull llama2` (or any other model)

**For Elastic Cloud (Expose Local Server):**

1. **Expose your local Ollama server** using one of these methods:

   **Method 1: Using ngrok (Recommended for testing)**
   ```bash
   # Install ngrok: https://ngrok.com/
   ngrok http 11434
   # Copy the HTTPS URL (e.g., https://abc123.ngrok.io)
   ```

   **Method 2: Using a reverse proxy (Production)**
   - Set up Nginx with SSL/TLS
   - Configure reverse proxy to forward to `localhost:11434`
   - Ensure proper authentication/security

2. **Configure the Connector:**
   - Select **OpenAI** connector
   - Under **Select an OpenAI provider**, choose **Other (OpenAI Compatible Service)**
   - **URL**: `https://your-ngrok-url.ngrok.io/v1/chat/completions` (or your reverse proxy URL)
   - **Default model**: `llama2` (or your model name)
   - **API Key**: Leave empty or use a token if you've configured authentication

**For On-Prem Elastic (Direct Connection):**

1. **Configure the Connector:**
   - Select **OpenAI** connector
   - Under **Select an OpenAI provider**, choose **Other (OpenAI Compatible Service)**
   - **URL**: `http://localhost:11434/v1/chat/completions` (or your server IP if on a different machine)
   - **Default model**: `llama2` (or your model name)
   - **API Key**: Leave empty

#### Option C: Local LLM with LM Studio

**Prerequisites:**
- Install [LM Studio](https://lmstudio.ai/) on your laptop
- Download and load a model in LM Studio
- Ensure the model name includes `instruct` (required for Elastic compatibility)

**For Elastic Cloud (Expose Local Server):**

1. **Set up LM Studio Server:**
   - Launch LM Studio GUI first (required for initial setup)
   - Start the server: `lms server start` (default port: 1234)
   - Or use the GUI to start the server

2. **Expose your local server** using ngrok or a reverse proxy:
   ```bash
   ngrok http 1234
   # Copy the HTTPS URL
   ```

3. **Configure the Connector:**
   - Select **OpenAI** connector
   - Under **Select an OpenAI provider**, choose **Other (OpenAI Compatible Service)**
   - **URL**: `https://your-ngrok-url.ngrok.io/v1/chat/completions`
   - **Default model**: `local-model` (or your model name)
   - **API Key**: Use a secret token if you've configured authentication in your reverse proxy

   > **Reference:** For detailed LM Studio setup instructions, see the [official Elastic documentation](https://www.elastic.co/docs/explore-analyze/ai-features/llm-guides/connect-to-lmstudio-security).

**For On-Prem Elastic (Direct Connection):**

1. **Configure the Connector:**
   - Select **OpenAI** connector
   - Under **Select an OpenAI provider**, choose **Other (OpenAI Compatible Service)**
   - **URL**: `http://localhost:1234/v1/chat/completions`
   - **Default model**: `local-model`
   - **API Key**: Leave empty

### Step 3: Test Your Connection

- Go to the **Test Tab** and click on the **Run** button

![Test Tab](images/image46.png)

- If the test is successful, you're ready to use the Elastic AI Assistant!
- If the test fails, check:
  - Your API key is correct (for cloud providers)
  - Your local server is running (for local LLMs)
  - Your URL is accessible (for cloud Elastic with local LLMs)
  - Firewall settings allow connections

![Connector Configuration](images/image47.png)

> **Security Note:** When exposing local servers publicly, always use proper authentication (API keys, tokens) and consider using HTTPS with SSL/TLS certificates for production environments.

## Chat With Your Data

Ask exactly the following questions to the Elastic AI Assistant.

### In Tamil

**Question 1**

```
எனது Kibana sample data logs-ல், response code 400-க்கு மேல் அல்லது சமமாக உள்ள rejected requests-களுக்கு காரணமான clientip addresses-ஐ எனக்கு சொல்ல முடியுமா? response.keyword field-ஐ பயன்படுத்தவும்.
```

**Question 2**

```
இந்த எண்களிலிருந்து ஒரு vertical bar graph-ஐ உருவாக்க முடியுமா?
```

### In Hindi

**Question 3**

```
clientip 30.156.16.164 से उत्पन्न logs से, इस IP address का भौगोलिक मूल स्थान खोजें।
```

**Question 4**

```
clientip 30.156.16.164 कौन से URLs तक पहुंचने की कोशिश कर रहा है? आप ESQL भाषा के CATEGORIZE directive का उपयोग कर सकते हैं।
```

### In English

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

> **Warning**: For each question, wait for the Elastic AI Assistant to deal completely with the answers before moving to the next question.

## Conclusion

In a nutshell, Elastic AI Assistant provides with the following benefits:

| Benefit | Description |
|---------|-------------|
| 👉 No need to know all field names | The assistant understands your intent and handles field discovery for you. |
| 👉 Automatically generates ESQL queries | Describe what you need and it writes the query for you. |
| 👉 Generates reusable visualizations | Quickly create graphs you can use in dashboards or refine further. |
| 👉 Explains ongoing incidents | Provides context and insights without manual investigation. |

---

# #4 ESQL: {ES\|QL}

Elasticsearch Query Language (ES\|QL) is Elastic's new piped language and query engine that transforms, enriches, and simplifies data investigations. The new ES\|QL query engine delivers advanced search capabilities with concurrent processing, improving speed and efficiency irrespective of data source and structure. It accelerates resolution by creating aggregations and visualizations from one screen delivering an uninterrupted workflow.

ES\|QL is a powerful investigative language that enables users to pivot around data, investigate patterns, and refine their analyses. It's not just for experts. Thanks to its built-in autocomplete capabilities, even less experienced users can get started easily.

## High Level Syntax

![ESQL Pattern Analysis](images/image48.png)

Start with a source command like `from` to query an index and evaluate data from there. Processing commands can be chained until achieving a desired output and limiting results as needed.

## Your First Query!

### Switch the Discover to ES\|QL

In the navigation pane, if you are not already in Discover, navigate to Discover in Kibana.

**Hint**:

- Open the hamburger menu and select Discover
- Select the **Kibana Sample Data Flights** dataview
- Click on the **Try ES\|QL** button at the top right

### Enter your 1st Query

`from` is the source command we will use for our first query. We'll keep it simple, the only processing command we will use is `limit`.

Create a query that gets 100 hits from the apache-logs index.

**Solution**:

```esql
FROM kibana_sample_data_flights
| LIMIT 1000
```

Don't forget to run the query using the play button!

## Query Filtering

`where` is the process command to use to filter a query.

Create a query that filters the flights that had a delay over the last 30000 hits. The concerned field is `FlightDelay:true`

**Solution**:

```esql
FROM kibana_sample_data_flights
| WHERE FlightDelay
| LIMIT 30000
```

or

```esql
FROM kibana_sample_data_flights
| WHERE FlightDelay=="True"
| LIMIT 30000
```

Don't forget to run the query using the play button!

## Query Aggregations

The command `stats...by...` is used to aggregate data according a key similar to a "group by" in SQL.

From the last query, iterate to compute:

- `delay` = the total sum of flight delay durations in minute (`FlightDelayMin`)
- `distance` = the total sum of distance miles (`DistanceMiles`)
- by delay type (`FlightDelayType`) and by carrier (`Carrier`)

**Documentation Link**

**Solution**:

```esql
from kibana_sample_data_flights
| where FlightDelay
| stats delay = sum(FlightDelayMin), distance=sum(DistanceMiles) by FlightDelayType, Carrier
| LIMIT 30000
```

Don't forget to run the query using the play button!

## Evaluation of New Additional Fields

Each iteration results in a number of rows. But at row level, we may compute additional fields. In previous query, we computed the total accumulated delay duration in minutes (`delay`) and the total cumulated corresponding flight distance in miles (`distance`) per type of delay and carrier.

The greater is the distance to fly, the more likely the flight is to experience a delay (longer distances requires heavier preparations, longer passenger onboarding, stronger requirements at take off..etc.

Thus, in order to compare the performance of an Airline regarding its delays, it is important to compute the following normalized ratio:

```
ratio=sum(FlightDelayMin)/sum(DistanceMiles)
```

Let's evaluate this ratio for each Carrier and FlightDelayType. To that aim, we are going to use the `eval` command.

**Documentation Link**

From previous query, iterate once again to compute that ratio in seconds per miles.

> **Warning**: As you are going to calculate a ratio, you should take care that the denominator is > 0. Before calling eval command, you may filter on `distance` > 0

**Solution**:

```esql
from kibana_sample_data_flights
| where FlightDelay
| stats delay = sum(FlightDelayMin), distance=sum(DistanceMiles) by FlightDelayType, Carrier
| where distance > 0
| eval ratio=delay/distance * 60
| LIMIT 30000
```

Don't forget to run the query using the play button!

## Results Presentation

### Refine our columns

Let's now sort the results by ratio desc and let's keep in the final results only the following columns `ratio`, `Carrier` and `FlightDelayType`.

To sort, you can use the `sort` command and to keep only the desired columns, you can use the `keep` command.

**Documentation Link for keep**  
**Documentation Link for sort**

Iterate on previous query to do so.

**Solution**:

```esql
from kibana_sample_data_flights
| where FlightDelay
| stats delay = sum(FlightDelayMin), distance=sum(DistanceMiles) by FlightDelayType, Carrier
| where distance > 0
| eval ratio=delay/distance * 60
| sort ratio desc
| keep Carrier, FlightDelayType, ratio
| LIMIT 30000
```

Don't forget to run the query using the play button!

### Change the visualization

By now, you should see the following visualization under the ESQL input panel. Click on the pencil icon:

![ESQL Visualization](images/image49.png)

Change the visualization to "Heatmap" as follow and click on the **Apply and Close** button

![Heatmap Visualization](images/image50.png)

<details>
<summary><strong>Question #1: What do you notice?</strong></summary>

**Answer to Question #1**:

- Kibana Airlines and JetBeats experience the highest delays, especially in Security, NAS, and Weather categories, as indicated by the darkest red shades (≥ 2.87 units).
- ES-Air shows elevated NAS delays, but no significant issues in other categories.
- JetBeats stands out with considerable Weather Delays, suggesting operational challenges in adverse conditions.
- Logstash Airways exhibits the least delay overall, especially in Security and NAS, indicating strong performance and possibly better coordination with airport authorities and ATC.
- Carrier and Late Aircraft Delays appear to be minimal or not reported for all airlines in this dataset.

</details>

![Heatmap Results](images/image51.png)

## Conclusion

ESQL is a powerful language that could be summarized by its benefits as follow:

| Benefit | Description |
|---------|-------------|
| 👉 Unified query language | Query across logs, metrics, traces, and more using a single syntax. |
| 👉 Powerful data exploration | Easily pivot across data, filter, aggregate, and correlate events. |
| 👉 Simplified syntax | Clean, readable syntax that's easier to learn than traditional query DSLs. |
| 👉 Autocomplete and guidance | Built-in IDE support helps beginners write accurate queries quickly. |
| 👉 Native integration with Kibana | Visualize results instantly or turn queries into reusable dashboards. |
| 👉 Supports advanced use cases | Perform joins, enrichments, subqueries, and inline calculations. |

---

# #5 From Discover to Alerting:

Discover is the easiest way to explore one's data but did you know it was also the easiest way to setup threshold-based alerts?

In this challenge, we are going to:

- setup an alert an cancelled flights
- format it and send it via email

To that aim, a smtp server connector has already been configured in your cluster.

## Cancelled Flights

- Go to the "Discover" and select "Kibana Sample Data Flights" dataview
- Set the time picker between the **The last 30 days** and **14 days from now**
- In the KQL bar, to display the cancelled flights, we just need to enter the following KQL expression:

```kql
Cancelled:true
```

- Your Discover should look like below:

![Cancelled Flights Discover](images/image52.png)

## Configure the Alert

- Click on "Alerts" and then "Create search threshold rule" as below:

![Create Alert](images/image53.png)

### Part 1: Configure Alert Schedule

We are going to configure the alert so that every 10 mn, it checks for any cancelled flight Make sure the alert is configured and named as follow:

![Alert Schedule Configuration](images/image54.png)

> **Warning**: Don't save yet

### Part 2: Configure Alert Actions (Notifications)

- Scroll down a little bit the alert configuration panel and add an "email" action by clicking on "email"

![Email Action](images/image55.png)

- The email configuration unfold. Please fill it as follow:

![Email Configuration](images/image56.png)

> **Warning**: Don't save yet

### Part 3: Customize Alert Notification Message

Replace the email **Message** placeholder with the following alert body:

```
**{{rule.name}}**

**Documents** _[view in Discover]({{{context.link}}})_

| **timestamp** | **Carrier** | **Flight Num** | **Origin to Destination** | \
{{#context.hits}}
| {{_source.timestamp}} | {{_source.Carrier}} |  {{_source.FlightNum}} | {{_source.OriginCityName}} ({{_source.OriginAirportID}}), {{_source.OriginWeather}}  ==>  {{_source.DestCityName}} ({{_source.DestAirportID}}), {{_source.DestWeather}}  | \
{{/context.hits}}
```

- You can now click on the alert **Save**
- Now, just wait for the alerts to popup in your email box!

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

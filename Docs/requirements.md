# Project Overview

ARCA Continental is embarking on an innovative project to co-develop an AI Sales Coach Proof of Concept (PoC) aimed at enhancing the efficiency and effectiveness of their sales team. This initiative is part of ARCA's broader strategy to leverage Agentic AI and advanced analytics to drive business growth and improve customer engagement. The AI Sales Coach will provide real-time assistance and insights to sales representatives, helping them to improve their skills, strategies, and overall performance.

Under this engagement, Microsoft will provide staff augmentation to ARCA to assist with the project.  ARCA is responsible for providing overall direction and control of the project team and its activities, managing the project schedule and estimates, and for overall project success.

Development of a Multi-Channel and Multi-Agent Architecture, starting with three agents, orchestrator Agent, Sales agent and Advanced Sales agent. Utilizing Microservices on AKS/ACA to facilitate and optimize various sales-related processes through a reliable and scalable workflow management system.

1. Subject Mater Expert Agents Descriptions

## 2.1. Orchestrator Agent

The Orchestrator Agent is the central coordinator of Arca Continental's multi-agent AI solution. It leverages multimodal capabilities, including text, audio, and image processing, to manage interactions and workflows between various specialized agents such as the Sales Agent, Product Agent, Order Agent, and Fulfillment Agent. This agent ensures seamless communication and integration, dynamically adjusting to various inputs and outputs to optimize overall sales operations. Acting as the brain of the system, it makes decisions based on comprehensive data analysis from all connected agents.

**Key Responsibilities:**

1. **Central Coordination:**
    - **Manage Interactions:** Facilitate and oversee interactions between different agents to ensure smooth and efficient communication.
    - **Workflow Management:** Coordinate workflows to optimize processes and ensure timely completion of tasks.
2. **Multimodal Capabilities:**
    - **Text Processing:** Analyze and interpret textual data from various sources to provide relevant insights and instructions.
    - **Audio Processing:** Utilize voice recognition and processing to handle verbal inputs and outputs, enhancing user interaction.
    - **Image Processing:** Analyze visual data to support decision-making and provide comprehensive insights.
3. **Data Integration and Analysis:**
    - **Aggregate Data:** Collect and integrate data from the Sales, Product, Order, and Fulfillment Agents to create a unified view of operations.
    - **Analyze Trends:** Identify trends and patterns in the data to inform strategic decisions and optimize sales operations.
    - **Decision Making:** Use data-driven insights to make informed decisions that enhance efficiency and performance.
4. **Dynamic Adjustment:**
    - **Adapt to Inputs:** Dynamically adjust to various inputs from different agents to ensure optimal performance.
    - **Optimize Outputs:** Modify outputs based on real-time data to improve overall sales operations and customer satisfaction.
5. **Communication and Integration:**
    - **Seamless Communication:** Ensure seamless communication between agents to avoid bottlenecks and enhance collaboration.
    - **System Integration:** Integrate with existing systems and platforms to provide a cohesive and efficient operational environment.
6. **Performance Monitoring:**
    - **Track Metrics:** Monitor key performance metrics to evaluate the effectiveness of the multi-agent system.
    - **Identify Issues:** Detect and address any issues or inefficiencies in the system to maintain high performance.

**Skills and Capabilities:**

- **Advanced Data Processing:** Proficient in handling and analyzing large datasets from multiple sources.
- **Multimodal Integration:** Skilled in integrating text, audio, and image data to provide comprehensive insights.
- **Strategic Decision Making:** Capable of making informed decisions based on data analysis to drive business growth.
- **Communication:** Effective in facilitating communication between agents and presenting data in a clear and actionable manner.
- **Adaptability:** Able to dynamically adjust to changing inputs and outputs to optimize performance.

## 2.2. Sales Agent

The Sales Agent is designed to assist in managing and analyzing sales data. This agent is equipped to handle a wide range of queries related to sales performance, customer behavior, product analysis, and market trends. The primary goal is to provide actionable insights that help optimize sales strategies, improve customer satisfaction, and drive business growth.

**Key Responsibilities:**

1. **Sales Volume Analysis:**
    - Calculate the total volume of bottles sold during specific periods (e.g., monthly, YTD, MTD, MoM, YoY, quarterly).
    - Track and compare sales volume evolution over time.
2. **Revenue and Profitability:**
    - Determine net revenues without taxes generated per month and by distribution channel.
    - Analyze the revenue share of each brand or brand aggregator.
    - Assess profitability by channel, zone, or product category.
3. **Channel and Zone Performance:**
    - Distribute sales among different commercial channels and identify the channel with the highest volume.
    - Identify zones generating the most sales in volume and revenue.
    - Compare current month's sales with the previous year and identify months with the highest growth.
4. **Customer Coverage and Loyalty:**
    - Measure customer or point-of-sale coverage and compare current coverage percentage against targets.
    - Evaluate route performance in terms of sales volume and delivery frequency.
    - Identify loyal customers with the highest purchases in the last quarter.
5. **Product Analysis:**
    - Identify best-selling products by volume and value.
    - Compare sales of returnable vs. non-returnable products.
    - Analyze the impact of promotions on sales volume for different zones, products, and customer types.
6. **Operational Efficiency:**
    - Track the number of active routes in each distribution center and the average volume handled by each route.
    - Identify territories with the highest number of billable customers and top products sold.
    - Monitor dispatch volumes and performance variations between different distribution centers.
7. **Forecasting and Trends:**
    - Project sales forecasts for upcoming periods based on historical sales data.
    - Identify products with marked seasonal trends and provide inventory recommendations.
    - Analyze brand penetration in subterritories compared to competitors.
8. **Customer Behavior and Risk Management:**
    - Identify customers who have decreased purchase frequency and may be at risk of loss.
    - Track the effectiveness of delivery times and average purchase ticket variations among different customer segments.
9. **Strategic Insights:**
    - Identify the most profitable customer segments and develop strategies to retain them.
    - Highlight areas where the sales force may need reinforcement to improve coverage.
    - Recommend marketing strategies for products with high growth potential.
10. **Performance Monitoring:**
    - Compare actual sales against forecasts for top products in each zone.
    - Identify zones with overestimated or underestimated demand.
    - Monitor the effectiveness of promotions by commercial channel.

**Skills and Capabilities:**

- **Data Analysis:** Ability to process and analyze large datasets to extract meaningful insights.
- **Trend Identification:** Skilled in identifying sales trends, seasonal patterns, and market opportunities.
- **Customer Insights:** Proficient in understanding customer behavior and loyalty metrics.
- **Operational Efficiency:** Capable of optimizing routes, delivery schedules, and inventory management.
- **Strategic Thinking:** Ability to provide strategic recommendations based on data analysis.
- **Communication:** Effective in presenting data and insights in a clear and actionable manner.

## 2.3. Product Agent

The Product Agent is a specialized AI solution designed to manage and provide detailed information about the Coca-Cola product catalog. This agent plays a crucial role in ensuring that product information is always up-to-date and accurate, thereby enhancing the customer experience and supporting sales efforts. It responds to queries from customers or sales representatives, helping them find the right products quickly and efficiently.

**Key Responsibilities:**

1. **Product Information Management:**
    - **Catalog Maintenance:** Maintain a comprehensive and up-to-date catalog of Coca-Cola products, including descriptions, prices, availability, and promotional offers.
    - **Data Accuracy:** Ensure that all product information is accurate and reflects the latest updates from the company.
2. **Customer and Sales Representative Support:**
    - **Query Response:** Respond to queries from customers and sales representatives regarding product details, availability, pricing, and promotions.
    - **Product Recommendations:** Provide personalized product recommendations based on customer preferences and sales data.
3. **Promotional Offers:**
    - **Promotion Management:** Track and manage promotional offers, ensuring that customers and sales representatives are aware of current deals and discounts.
    - **Impact Analysis:** Analyze the impact of promotions on sales and customer engagement.
4. **Availability and Inventory Tracking:**
    - **Stock Levels:** Monitor product availability and stock levels across different distribution centers.
    - **Restocking Alerts:** Generate alerts for restocking products that are running low to ensure continuous availability.
5. **\`**
    - **Sales Support:** Collaborate with the Sales Agent to provide detailed product information that supports sales strategies and operations.
    - **Order Processing:** Assist in the order processing workflow by providing accurate product details and availability information.

6. Data Retrieval and Advanced Reasoning Agents Descriptions



    
The following diagram shows the logical flow within mutlti agent system. In an ideal scenario, the questions will follow the _Pre-Fetched Cache Results Path** which leads to the quickest answer generation. In cases where the question is not known, the group chat selector will fall back to the other agents accordingly and generate the SQL query using the LLMs. The cache is then updated with the newly generated query and schemas.

![Agentic Text2SQL Query Cache](./Agentic%20Text2SQL%20Query%20Cache.png)

Unlike the previous approaches, **gpt4o-mini** can be used as each agent's prompt is small and focuses on a single simple task.

As the query cache is shared between users (no data is stored in the cache), a new user can benefit from the pre-mapped question and schema resolution in the index.

**Database results were deliberately not stored within the cache. Storing them would have removed one of the key benefits of the Text2SQL plugin, the ability to get near-real time information inside a RAG application. Instead, the query is stored so that the most-recent results can be obtained quickly. Additionally, this retains the ability to apply Row or Column Level Security.**

**Agents**

This agentic system contains the following agents:

- **Query Cache Agent:** Responsible for checking the cache for previously asked questions.
- **Query Decomposition Agent:** Responsible for decomposing complex questions, into sub questions that can be answered with SQL.
- **Schema Selection Agent:** Responsible for extracting key terms from the question and checking the index store for the queries.
- **SQL Query Generation Agent:** Responsible for using the previously extracted schemas and generated SQL queries to answer the question. This agent can request more schemas if needed. This agent will run the query.
- **SQL Query Verification Agent:** Responsible for verifying that the SQL query and results question will answer the question.
- **Answer Generation Agent:** Responsible for taking the database results and generating the final answer for the user.

The combination of this agent allows the system to answer complex questions, whilst staying under the token limits when including the database schemas. The query cache ensures that previously asked questions, can be answered quickly to avoid degrading user experience.

**Parallel execution**

After the first agent has rewritten and decomposed the user input, we execute each of the individual questions in parallel for the quickest time to generate an answer.

**Caching Strategy**

The cache strategy implementation is a simple way to prove that the system works. You can adopt several different strategies for cache population. Below are some of the strategies that could be used:

- **Pre-population:** Run an offline pipeline to generate SQL queries for the known questions that you expect from the user to prevent a 'cold start' problem.
- **Chat History Management Pipeline:** Run a real-time pipeline that logs the chat history to a database. Within this pipeline, analyse questions that are Text2SQL and generate the cache entry.
- **Positive Indication System:** Only update the cache when a user positively reacts to a question e.g. a thumbs up from the UI or doesn't ask a follow up question.
- **Always update:** Always add all questions into the cache when they are asked. The sample code in the repository currently implements this approach, but this could lead to poor SQL queries reaching the cache. One of the other caching strategies would be better production version.
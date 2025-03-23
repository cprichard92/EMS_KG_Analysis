# EMS Dispatch Visual Data Analysis

## Overview

This project provides a comprehensive dataset of 911 dispatch records along with example queries for Neo4j. The research aims to optimize EMS resource allocation by distinguishing true emergencies from non-emergency calls. With 911 overwhelmed by non-emergency calls, this project helps identify patterns in dispatch data, enabling better decision-making and potential alternative solutions such as community paramedicine.

## Research Purpose

The core problem is that 911 systems are burdened with non-emergency calls that drain critical resources and delay responses to real crises. By analyzing dispatch data, including ICD-10 codes and location details, this research examines the misallocation of EMS resources. The project provides insights into which calls are truly emergent versus those that could be handled by primary care or community paramedics, thereby improving overall EMS efficiency.

Link to research paper: https://docs.google.com/document/d/1OKK0xXJC-a6cZ2NhYfMT5iY6ssbUQOmJ/edit?usp=sharing&ouid=111753167936409076251&rtpof=true&sd=true

## Dataset

The dataset contains 50,000 records of simulated 911 dispatch calls. Each record includes:
- **Call_ID:** Unique identifier for the call.
- **Date_Time:** Timestamp of the call.
- **Dispatch_Code:** Code representing the type of dispatch.
- **Disposition_Code:** Outcome of the call (e.g., transported, treated on scene).
- **ICD-10_Code:** ICD-10 medical code representing the condition.
- **District:** Geographic area where the call originated.
- **Sub_Division:** Subdivision within the district.
- **Street_Name:** The street where the call was made.
- **Latitude & Longitude:** Geolocation data.
- **Mutual_Aid_District:** Indicates if the call was handled as mutual aid.
- **Hospital:** Destination hospital (if applicable).
- **Emergency_Type:** Categorizes the call as "Emergent" or "Non-Emergent".

## How to Set Up and Run Locally

### Prerequisites

- **Neo4j Desktop:** Install Neo4j Desktop from [neo4j.com/download](https://neo4j.com/download/).
- **Git:** Ensure Git is installed on your machine.
- **Python (Optional):** If you need to regenerate or modify the dataset.

### Instructions

1. **Clone the Repository:**

   ```bash
   git clone https://github.com/yourusername/ems-dispatch-data.git
   cd ems-dispatch-data
   ```

2. **Review the Dataset:**

   The dataset is provided as an Excel file (`911_dispatch_data_with_severity.xlsx`). If needed, convert it to CSV format for Neo4j import:
   
   - Open the Excel file and save as CSV (e.g., `911_dispatch_data_with_severity.csv`).

3. **Place the CSV in Neo4j Import Directory:**

   - Locate your Neo4j import folder (e.g., `/path/to/neo4j/import`).
   - Move the CSV file into this folder.

4. **Import Data into Neo4j:**

   Open Neo4j Browser and run the following query:
   
   ```cypher
   LOAD CSV WITH HEADERS FROM 'file:///911_dispatch_data_with_severity.csv' AS row
   CREATE (:Dispatch {
       call_id: row.Call_ID,
       date_time: row.Date_Time,
       dispatch_code: row.Dispatch_Code,
       disposition_code: row.Disposition_Code,
       icd10_code: row.`ICD-10_Code`,
       emergency_type: row.Emergency_Type,
       district: row.District,
       sub_division: row.Sub_Division,
       street_name: row.Street_Name,
       latitude: toFloat(row.Latitude),
       longitude: toFloat(row.Longitude),
       mutual_aid_district: row.Mutual_Aid_District,
       hospital: row.Hospital
   });
   ```

5. **Run Introductory Queries:**

   Here are some sample queries to explore the data:
   
   - **Visualize Dispatches in Central Zone:**
     ```cypher
     MATCH (Dispatch)-[:OCCURRED_IN]->(District {name: "Central Zone"})
     RETURN Dispatch, District LIMIT 20;
     ```
   
   - **Aggregate ICD-10 Conditions by Call Volume:**
     ```cypher
     MATCH (Dispatch)-[:RELATED_TO]->(MedicalCondition)
     WITH MedicalCondition, COUNT(Dispatch) AS call_count
     RETURN MedicalCondition.code, MedicalCondition.description, call_count
     ORDER BY call_count DESC LIMIT 10;
     ```
   
   - **Compare Emergent vs. Non-Emergent Calls in Central Zone:**
     ```cypher
     MATCH (Dispatch)-[:OCCURRED_IN]->(District {name: "Central Zone"})
     MATCH (Dispatch)-[:RELATED_TO]->(MedicalCondition)
     WHERE Dispatch.emergency_type IN ["Emergent", "Non-Emergent"]
     WITH Dispatch.emergency_type AS Type, COUNT(Dispatch) AS Total
     RETURN Type, Total;
     ```

6. **Visual Exploration:**

   - Use Neo4j Bloom (available in Neo4j Desktop) for an interactive graph view.
   - Experiment with expanding and collapsing nodes to explore relationships between Districts, ICD-10 conditions, and other entities.

## Synopsis of the Research

The project's goal is to provide actionable insights into the misuse of 911 services by analyzing dispatch data. With an overwhelming number of non-emergency calls burdening the system, the research demonstrates how data-driven approaches can guide better resource allocation, reducing unnecessary EMS deployments and enabling more targeted community paramedicine interventions.

## Contributing and Feedback

Feel free to fork the repository, submit pull requests, or open issues if you have suggestions or improvements. Your feedback is invaluable to refine the project and further validate the research insights.

---

Happy analyzing!

---


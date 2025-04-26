# Gun Violence Data Warehouse and Analysis
![Gun Violence Data Warehouse Schema](DW_Schema.png)

## Overview
This repository contains the implementation of a comprehensive data warehouse project conducted as part of the Master's Degree in Data Science and Business Informatics at the University of Pisa. The project, titled "Gun Violence Data Warehouse," focuses on building a robust data warehouse to store and analyze gun violence incidents in the United States from January 2013 to March 2018. It leverages SQL Server Management Studio (SSMS), SQL Server Integration Services (SSIS), and multidimensional data analysis techniques to process, integrate, and analyze data, providing insights into crime gravity, youth criminality, and gun-related incidents. The project is divided into three main parts: data warehouse construction, SSIS-based data flows, and multidimensional analysis with Power BI visualizations.

---

## Problem Statement
The primary objective is to design and implement a data warehouse to organize gun violence data, enabling efficient querying and analysis. The project addresses challenges such as data preprocessing, schema design, efficient data population, and analytical query optimization. It is structured into three key parts:

1. **Part 1: Data Warehouse Construction**: Build a data warehouse using SSMS, preprocess raw data (Police.csv, dates.xml), and populate tables (Custody, Gun, Participant, Date, Incident, Geography) with optimized ID handling and crime gravity computation.
2. **Part 2: SSIS Data Flows**: Develop SSIS packages to compute annual custody counts, identify states with youth criminal problems (age < 18), and analyze crime gravity ratios for stolen vs. non-stolen guns.
3. **Part 3: Multidimensional Data Analysis**: Create an OLAP cube with hierarchies and measures for crime gravity analysis, execute MDX queries, and build Power BI dashboards to visualize geographical and categorical crime distributions.

The project aims to provide a scalable framework for analyzing gun violence trends, supporting law enforcement and policymakers with actionable insights.

---

## Dataset
The dataset comprises gun violence data from January 2013 to March 2018, primarily sourced from two files:
- **Police.csv**: Contains 170,928 rows and 11 columns, including:
  - **Attributes**: custody_id (170,928 unique), incident_id (105,168 unique), date_fk (1,634 unique), latitude (65,553 unique), longitude (68,607 unique), and categorical attributes (e.g., participant age, status, type).
  - **No missing values** observed.
- **Dates.xml**: Maps date_fk to actual dates for temporal analysis.
- **Dictionaries**: JSON files (participant_age.json, participant_status.json, participant_type.json) used to compute crime gravity.

The data is processed into six relational tables (Custody, Gun, Participant, Date, Incident, Geography) within the GroupID_200_DB database, hosted on the server `lds.di.unipi.it`.

---

## Project Structure
The repository (`Gun_Violence_Data_Warehouse_SQL`) contains the following files and directories:

- **`LDS_200_part1/`**:
  - Contains scripts and resources for Part 1, including SQL scripts for schema creation and Python scripts for data preprocessing and population.

- **`LDS_Part2_Group_Id_200/`**:
  - Includes SSIS packages (`Assignment_0.dtsx`, `Assignment_1.dtsx`, `Assignment_2.dtsx`) for data flow tasks, computing custody counts, youth criminality, and crime gravity ratios.

- **`LDS_Part3_Group_Id_200/`**:
  - Contains resources for multidimensional analysis, including MDX queries (`Project-3.mdx`) and Power BI dashboard configurations.

- **`Project_Report.pdf`**:
  - The comprehensive project report detailing methodologies, implementation details, results, and visualizations for all three parts of the project.

- **`README.md`**:
  - The main README file providing an overview of the project, problem statement, dataset details, analysis phases, key findings, tools, and usage instructions.

---

## Analysis and Insights
The project is structured into three analytical parts, each leveraging specific tools and techniques:

### Part 1: Data Warehouse Construction
- **Task**: Build and populate a data warehouse (`GroupID_200_DB`) with six tables.
- **Preprocessing**:
  - Parsed `Police.csv` and `dates.xml` using Python (without pandas) to split data into tables.
  - Computed crime gravity using dictionaries and the `compute_crime_gravity` function.
  - Retrieved location data (city, state) using the Google Maps Geocoding API with retry logic.
- **Schema Creation**:
  - Created tables (Custody, Gun, Participant, Date, Incident, Geography) in SSMS with appropriate relationships (see Figure 1 in report).
  - Used `IDENTITY(1,1)` for auto-incremental IDs (`gun_id`, `participant_id`, `geo_id`).
- **Data Population**:
  - **Initial Attempt**: Simultaneous upload of 170,928 records was slow (>3 days) and prone to interruptions.
  - **Second Attempt**: Leveraged SSMS auto-incremental IDs, improving efficiency.
  - **Third Attempt**: Batched uploads (1,000 records) and skipped city/state details, reducing upload time to <2 hours.
  - **Fourth Attempt**: Optimized location data retrieval using Google Maps API, ensuring speed and accuracy.
- **Key Insights**: Batch processing and SSMS auto-incremental IDs significantly enhanced population efficiency. Google Maps API improved geocoding accuracy.

### Part 2: SSIS Data Flows
- **Task**: Develop SSIS packages for analytical queries.
- **Assignment 0**: Compute annual custody counts.
  - Used OLE DB Source, Lookup, Aggregation, and Flat File Destination to output `Assignment_0.csv` with year and custody count.
- **Assignment 1**: Identify states with youth criminal problems (age < 18).
  - Used Lookup, Multicast, Aggregation, Merge Join, and Conditional Split to filter high-gravity records, outputting `Assignment_1_full.csv` (detailed) and `Assignment_1_states.csv` (state list).
- **Assignment 2**: Analyze crime gravity ratios for stolen vs. non-stolen guns.
  - Used Conditional Split, Aggregation, Merge Join, and Derived Column to compute `tcg_ratio` (stolen/non-stolen gravity).
  - Output three files: `Assignment_2_full.csv` (all records), `Assignment_2_with_ratio.csv` (with ratio), and `Assignment_2_without_ratio.csv` (without ratio).
- **Key Insights**: SSIS enabled efficient data flow management. Conditional Split and Derived Column transformations were critical for nuanced analyses like youth criminality and gun status.

### Part 3: Multidimensional Data Analysis
- **OLAP Cube Creation**:
  - Built an OLAP cube with dimensions (Date, Geography, Gun, Incident, Participant) and measures (Crime_Gravity, Custody_Count).
  - Defined hierarchies: Date (Year → Quarter → Month), Geography (Continent → Country → State → City), Gun (Is Stolen → Gun Type).
- **MDX Queries**:
  - **Assignment 0**: Defined measures (`prev_year_crime_gravity`, `percent_diff`, `total_crime_gravity`, `Average_Gravity_Score`) for dynamic analysis.
  - **Assignment 1**: Computed percentage change in crime gravity by state, revealing annual trends.
  - **Assignment 2**: Calculated each gun’s crime gravity percentage relative to the total, identifying high-impact guns.
  - **Assignment 3**: Filtered incidents with gravity ≥ average state gravity, highlighting severe incidents.
- **Power BI Dashboards**:
  - **Assignment 4**: Visualized geographical crime gravity distribution by age group using an ArcGIS map and donut chart, with interactive state-specific details.
  - **Assignment 5**: Created a custom dashboard with slicers (age group, year), a stacked bar chart (crime gravity by gun type/status), and a donut chart (gun status distribution), enabling dynamic exploration.
- **Key Insights**: MDX queries provided granular insights into crime trends. Power BI dashboards enhanced interpretability with interactive visualizations.

---

## Key Findings
- **Data Warehouse**: Batch processing and SSMS auto-incremental IDs reduced population time from >3 days to <2 hours. Google Maps API ensured accurate geocoding.
- **SSIS Data Flows**: Multicast and Conditional Split transformations enabled flexible analysis of youth criminality and gun status, with automated CSV outputs.
- **Multidimensional Analysis**: OLAP cube hierarchies (Date, Geography) and MDX measures (`percent_diff`, `total_crime_gravity`) revealed state-level crime trends and gun impacts. Power BI dashboards provided intuitive, interactive insights.
- **Applications**: The framework supports targeted interventions by identifying high-gravity incidents, youth criminality hotspots, and stolen gun impacts.

---

## Tools and Technologies
- **Programming Languages**: SQL, Python
- **Tools**: SQL Server Management Studio (SSMS), SQL Server Integration Services (SSIS), Visual Studio 2019, Power BI
- **Libraries**: Python datetime, Google Maps Geocoding API
- **Techniques**: Data warehousing, schema design, data preprocessing, geocoding, SSIS data flows, OLAP cube creation, MDX querying, dashboard visualization

---

## How to Use
1. Clone the repository:
   ```bash
   git clone https://github.com/Mohamed-Arafaath/Gun_Violence_Data_Warehouse_SQL.git
   ```
2. Navigate to the respective folders (`LDS_200_part1`, `LDS_Part2_Group_Id_200`, `LDS_Part3_Group_Id_200`) to access scripts and SSIS packages.
3. Set up the database on `lds.di.unipi.it` using SSMS and the provided SQL scripts in `LDS_200_part1`.
4. Run SSIS packages in Visual Studio 2019 from `LDS_Part2_Group_Id_200`.
5. Execute MDX queries and explore Power BI dashboards from `LDS_Part3_Group_Id_200`.
6. Refer to `Project_Report.pdf` for detailed methodologies and results.

---

## 📧 Contact
For any questions or feedback, feel free to reach out:

- **Author**: [Mohamed Arafaath](https://www.linkedin.com/in/mohamed-arafaath/)
- **Email**: mohamed_arafaath@outlook.com

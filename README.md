# Home_Sales

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

## Overview

This repository contains a Jupyter Notebook (`Home_Sales.ipynb`) that uses PySpark to analyze home sales data.  The notebook performs several tasks, including:

1.  Reading data from an AWS S3 bucket.
2.  Creating temporary views and tables in Spark.
3.  Executing SparkSQL queries to answer specific questions about the data.
4.  Caching and uncaching Spark tables.
5.  Partitioning data using Parquet format.
6. Comparing query runtimes for cached, uncached, and partitioned data.

The project demonstrates practical applications of Spark for data manipulation, analysis, and performance optimization. A detailed explanation of the code is available in `Home_Sales_ipynb_Explanation.pdf`.

## Repository Structure and File Descriptions

This repository is organized as follows:

```
Home_Sales/
├── Home_Sales.ipynb
├── Home_Sales_ipynb_Explanation.pdf
└── README.md
```

*   **`Home_Sales.ipynb`**: (Located at the root of the repository)
    This is the main Jupyter Notebook containing the PySpark code for this project.  It is designed to be run in Google Colab and includes all the necessary steps, from installing Spark to performing the data analysis.  The notebook is divided into clearly marked sections corresponding to the instructions in the assignment, making it easy to follow the logic and understand the code.  Key functionalities include:

    *   **Spark and Java Installation:** Sets up the necessary environment for running Spark in Colab.
    *   **Data Loading:** Reads the `home_sales_revised.csv` dataset from the specified AWS S3 bucket.
    *   **Temporary View Creation:** Creates a temporary Spark SQL view named `home_sales`.
    *   **SparkSQL Queries:** Executes a series of SQL queries to answer specific questions about the dataset (average prices based on different criteria).
    *   **Caching:** Caches the `home_sales` table to improve query performance.
    *   **Partitioning:** Writes the data to a Parquet file, partitioned by the `date_built` column, and creates a temporary view `p_home_sales`.
    *   **Runtime Comparisons:** Measures and compares the execution time of queries on uncached, cached, and partitioned data.
    *   **Uncaching:** Removes the `home_sales` table from the cache.

*   **`Home_Sales_ipynb_Explanation.pdf`**: (Located at the root of the repository)
    This PDF document provides a detailed, step-by-step explanation of the code within the `Home_Sales.ipynb` notebook. This includes explanations of each code cell, the Spark and SparkSQL concepts used, and the reasoning behind the chosen approach. It serves as a companion document to help understand the *why* behind the code, not just the *what*.  The explanations cover:
    * Setup steps in detail
    * Rationale behind Spark configurations
    * Purpose and effects of caching and partitioning
    * Line-by-line breakdown of SparkSQL queries
    * Interpretation of results

*   **`README.md`**: (Located at the root of the repository)
    This file (the one you are currently reading) provides a comprehensive overview of the project, its purpose, the data used, instructions for setting up and running the notebook, details about the repository structure, and licensing information.

* **`LICENSE`**: (Located at the root of the repository)
    This file contains the text of the MIT license under which this project is released.

## Data Source

The data used in this project is the `home_sales_revised.csv` file, located in a public AWS S3 bucket.  The specific URL is included within the `Home_Sales.ipynb` notebook. The dataset contains information about home sales, including:

*   `id`: Unique identifier for the home sale.
*   `date`: Date of the sale.
*   `date_built`: Year the home was built.
*   `price`: Sale price of the home.
*   `bedrooms`: Number of bedrooms.
*   `bathrooms`: Number of bathrooms.
*   `sqft_living`: Square footage of the living space.
*   `sqft_lot`: Square footage of the lot.
*   `floors`: Number of floors.
*   `waterfront`: Whether the home has a waterfront view (1 for yes, 0 for no).
*   `view`: A rating of the home's view.

## Setup and Execution

### Prerequisites

*   A Google account to use Google Colab.
* Basic knowledge of the following tools, libraries and languages:
    *  Google Colab
    *  PySpark
    *  SparkSQL
    *  AWS S3
    *  Jupyter Notebooks
    *  Parquet
    *  Markdown
    *  YAML

### Running the Notebook

1.  **Open `Home_Sales.ipynb` in Google Colab:**  You can either upload the notebook directly to your Google Drive and open it from there, or you can use the "File" -> "Upload Notebook" option in Colab.
2.  **Run all cells:**  Select "Runtime" -> "Run all" from the Colab menu.  This will execute all the code cells in the notebook sequentially.

The notebook is self-contained and includes all necessary setup steps, such as installing Spark and setting environment variables.  No external setup is required beyond having a Google account.

## SparkSQL Queries

The notebook answers the following questions using SparkSQL:

1.  **Average price of four-bedroom houses sold each year:**
    ```sql
    SELECT YEAR(date) AS sale_year, ROUND(AVG(price), 2) AS average_price
    FROM home_sales
    WHERE bedrooms = 4
    GROUP BY sale_year
    ORDER BY sale_year;
    ```

2.  **Average price of homes with three bedrooms and three bathrooms for each year built:**
    ```sql
    SELECT date_built, ROUND(AVG(price), 2) AS average_price
    FROM home_sales
    WHERE bedrooms = 3 AND bathrooms = 3
    GROUP BY date_built
    ORDER BY date_built;
    ```

3.  **Average price of homes with three bedrooms, three bathrooms, two floors, and at least 2000 sqft, for each year built:**
    ```sql
    SELECT date_built, ROUND(AVG(price), 2) AS average_price
    FROM home_sales
    WHERE bedrooms = 3 AND bathrooms = 3 AND floors = 2 AND sqft_living >= 2000
    GROUP BY date_built
    ORDER BY date_built;
    ```

4.  **Average price of homes per "view" rating with an average price of at least $350,000, ordered by view (descending):**
    ```sql
    SELECT view, ROUND(AVG(price), 2) AS average_price
    FROM home_sales
    GROUP BY view
    HAVING AVG(price) >= 350000
    ORDER BY view DESC;
    ```
    This query is also used to compare runtimes for cached, uncached, and partitioned data.

## Caching and Partitioning

The notebook demonstrates caching and partitioning to improve query performance:

*   **Caching:**  The `home_sales` temporary table is cached using `spark.sql("CACHE TABLE home_sales")`.  The runtime of the "view" query is then compared before and after caching.
*   **Partitioning:** The data is partitioned by the `date_built` column and saved in Parquet format using `df.write.partitionBy("date_built").mode("overwrite").parquet("home_sales_partitioned")`. A new temporary table `p_home_sales` is created from the partitioned data. The "view" query is executed on this partitioned data, and the runtime is compared to the uncached and cached versions.

## License

This project is licensed under the MIT License.

```text
MIT License

Copyright (c) 2025 Alex Gerwer

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

## Acknowledgements

*   Data for this dataset was generated by edX Boot Camps LLC, and is intended for educational purposes only.
*   This project was completed as part of a Data Analytics Bootcamp.
*   The project utilizes Apache Spark and PySpark.

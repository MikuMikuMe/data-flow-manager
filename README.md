# data-flow-manager

Below is a complete Python program for a Data Flow Manager that automates data ETL (Extract, Transform, Load) processes. This program is designed to handle data extraction from a source, transform the data, and load it into a target system. The tool includes error handling to ensure robustness and inform users of any issues encountered during the ETL process. Note that this example is simplified and assumes that both the source and target data are managed through CSV files, which are common in many ETL pipelines.

```python
import csv
import logging
import os
from typing import List, Dict

# Configure logging
logging.basicConfig(level=logging.INFO, format='%(asctime)s - %(levelname)s - %(message)s')

class DataFlowManager:
    def __init__(self, source_file: str, target_file: str):
        self.source_file = source_file
        self.target_file = target_file

    def extract(self) -> List[Dict[str, str]]:
        """Extract data from the source file."""
        try:
            logging.info(f"Extracting data from {self.source_file}.")
            with open(self.source_file, mode='r', newline='', encoding='utf-8') as file:
                reader = csv.DictReader(file)
                data = [row for row in reader]
                logging.info("Data extraction completed successfully.")
                return data
        except FileNotFoundError:
            logging.error(f"Source file {self.source_file} not found.")
            raise
        except Exception as e:
            logging.error(f"An error occurred during extraction: {e}")
            raise

    def transform(self, data: List[Dict[str, str]]) -> List[Dict[str, str]]:
        """Transform the data."""
        try:
            logging.info("Transforming data.")
            transformed_data = []
            for row in data:
                # Example transformation: capitalize all string values
                transformed_row = {key: value.upper() if isinstance(value, str) else value for key, value in row.items()}
                transformed_data.append(transformed_row)
            logging.info("Data transformation completed successfully.")
            return transformed_data
        except Exception as e:
            logging.error(f"An error occurred during transformation: {e}")
            raise

    def load(self, data: List[Dict[str, str]]):
        """Load data into the target system."""
        try:
            logging.info(f"Loading data into {self.target_file}.")
            with open(self.target_file, mode='w', newline='', encoding='utf-8') as file:
                if not data:
                    logging.warning("No data to load.")
                    return
                writer = csv.DictWriter(file, fieldnames=data[0].keys())
                writer.writeheader()
                writer.writerows(data)
                logging.info("Data loading completed successfully.")
        except Exception as e:
            logging.error(f"An error occurred during loading: {e}")
            raise

    def run_etl_process(self):
        """Run the full ETL process: Extract, Transform, Load."""
        logging.info("ETL process started.")
        try:
            data = self.extract()
            transformed_data = self.transform(data)
            self.load(transformed_data)
        except Exception as e:
            logging.error(f"ETL process failed: {e}")
        else:
            logging.info("ETL process completed successfully.")

if __name__ == "__main__":
    # Specify paths to the source and target CSV files
    source_path = 'source_data.csv'
    target_path = 'target_data.csv'

    # Create an instance of the DataFlowManager
    dfm = DataFlowManager(source_path, target_path)

    # Run the ETL process
    dfm.run_etl_process()
```

### Program Explanation
- **Logging:** The `logging` module is configured to log information about the process at various stages. This includes information-level logs for normal operations and error-level logs for exception cases.
  
- **Error Handling:** The program uses `try-except` blocks to handle exceptions during the ETL phases (Extract, Transform, Load).

- **ETL Phases:**
  - **Extract:** Reads data from a CSV file using `csv.DictReader` and stores it in a list of dictionaries.
  - **Transform:** Example transformation is capitalizing strings; this can be customized according to the specific needs.
  - **Load:** Writes the transformed data to another CSV file using `csv.DictWriter`.

- **ETL Execution:** The ETL process is initiated via the `run_etl_process` method, which sequentially calls `extract`, `transform`, and `load` methods. It is equipped with logging to track the progress and issues.

The program is basic and uses CSV files for simplicity. In real-world scenarios, you might interface with databases or other storage solutions. You'd also customize transformations to suit your specific data processing requirements.
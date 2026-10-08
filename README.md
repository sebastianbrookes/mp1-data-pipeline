# MP1 Data Pipeline

This pipeline is a command-line tool. It loads a CSV dataset, validates it, cleans it using settings from a YAML config file, and writes the cleaned data to a new CSV. `pipeline.py` coordinates the run: it parses the arguments, sets up logging, checks that the input and config files exist, and then passes the data through each stage. The modules live in the `src/` package, and each one handles a single stage. `data_loaders.py` reads CSV, JSON, or YAML files based on their extension. `data_validator.py` checks that the required columns are present and drops rows whose numeric columns contain values that can't be parsed as numbers. `data_processor.py` removes duplicates, rows with missing values, and outliers (IQR or z-score), and `create_cleaning_report()` counts how many rows and columns were removed. `data_output.py` saves the cleaned DataFrame, creating the output directory if it doesn't exist, and `utils.py` holds the shared logging setup and input-path checks. The required columns, numeric columns, and cleaning steps are set in `config/config.yaml`, so the pipeline can run on a different dataset without code changes.

## Example

```bash
python pipeline.py --input fixtures/sample_data.csv --output output/clean.csv --config config/config.yaml --verbose
```

Output:

```text
16:42:29 DEBUG    __main__ — Arguments parsed: input=fixtures/sample_data.csv, config=config/config.yaml, output=output/clean.csv
16:42:29 INFO     src.utils — Input file validated: fixtures/sample_data.csv
16:42:29 INFO     src.utils — Input file validated: config/config.yaml
16:42:29 INFO     src.data_loaders — Loaded CSV file: fixtures/sample_data.csv (100 rows)
16:42:29 INFO     src.data_loaders — Loaded YAML file: config/config.yaml
16:42:29 WARNING  src.data_validator — Invalid numeric value in rating at row 94: 'not_available'
16:42:29 WARNING  src.data_validator — Invalid numeric value in rating at row 95: 'error'
16:42:29 WARNING  src.data_validator — Removed 2 rows with invalid numeric values in rating
16:42:29 DEBUG    src.data_validator — Validation: 100 -> 98 rows (2 removed)
16:42:29 INFO     __main__ — Validation complete: 100 → 98 rows
16:42:29 DEBUG    src.data_processor — Removed 2 duplicate row(s)
16:42:29 DEBUG    src.data_processor — handle_missing: 96 → 94 rows
16:42:29 DEBUG    src.data_processor — rating: method=iqr, threshold=1.5, removed=2
16:42:29 INFO     __main__ — Processing complete: 98 → 92 rows
16:42:29 DEBUG    src.data_output — Saved 92 rows to output/clean.csv
16:42:29 INFO     __main__ — Saved cleaned data to output/clean.csv

Cleaning report:
{'rows_before': 98,
 'rows_after': 92,
 'rows_removed': 6,
 'columns_before': 5,
 'columns_after': 5,
 'columns_removed': 0}
```

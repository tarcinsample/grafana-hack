# Hackathon Scoring & Leaderboard Dashboard

This project provides a complete setup for monitoring a hackathon's scoring process using Grafana. It includes sample data files and a pre-configured Grafana dashboard JSON file.

## Overview

The goal of this project is to provide a real-time, data-driven overview of a hackathon. It allows organizers, judges, and participants to track scores, view leaderboards, and gain insights into the judging process.

The system is designed to be data source agnostic. While sample CSV files are provided, the dashboard queries can be easily adapted to any SQL-like database (e.g., PostgreSQL, MySQL) where the hackathon data is stored.

## Files Included

- **/participants.csv**: Contains information about each team.
- **/judges.csv**: A list of the judges.
- **/criteria.csv**: The scoring criteria and their respective weightage.
- **/scores.csv**: Individual scores given by judges to teams for each criterion.
- **/bonus.csv**: Bonus points awarded to teams for special events.
- **/hackathon_dashboard.json**: The Grafana dashboard file.

---

## Prerequisites

Before you begin, ensure you have the following installed and running:
1.  **Grafana**: Version 9.0 or newer.
2.  **Grafana CSV Plugin**: This plugin is required to use the CSV files directly as a data source. You can install it via the Grafana UI or using the `grafana-cli`:
    ```bash
    grafana-cli plugins install grafana-csv-plugin
    ```
    After installation, you will need to restart your Grafana server.

---

## Usage Instructions

Follow these steps to get the dashboard running.

### Step 1: Place CSV Files

Create a directory on the server where Grafana is running. Place all the `.csv` files (`participants.csv`, `judges.csv`, `criteria.csv`, `scores.csv`, `bonus.csv`) into this directory.

For example: `/var/lib/grafana/csv_data/`

**Important**: Ensure that the Grafana user has read permissions for this directory and the files within it.

### Step 2: Configure the CSV Data Source in Grafana

1.  Log in to your Grafana instance.
2.  Navigate to **Connections** > **Data sources**.
3.  Click **Add new data source** and search for **CSV**.
4.  Select the CSV data source plugin.
5.  In the configuration page, under the **Path** setting, enter the absolute path to the directory where you placed the CSV files (e.g., `/var/lib/grafana/csv_data/`).
6.  Click **Save & test**. Grafana should confirm that the data source is working.

*Note: The dashboard JSON provided uses the Grafana TestData source for portability. You will need to manually switch each panel to use your newly configured CSV data source.*

### Step 3: Import the Grafana Dashboard

1.  Navigate to **Dashboards**.
2.  Click on the **New** button and select **Import**.
3.  Click the **Upload JSON file** button and select the `hackathon_dashboard.json` file provided in this repository.
4.  On the next screen, you can change the dashboard name if you wish.
5.  Click **Import** to finish the process.

### Step 4: Link Panels to Your Data Source (If Needed)

The provided `hackathon_dashboard.json` uses dummy data to ensure it can be imported without a pre-existing data source. You will need to associate each panel with the CSV source you created in Step 2.

For each panel in the dashboard:
1.  Click the title of the panel and select **Edit**.
2.  In the query editor, select your CSV data source from the dropdown menu at the top.
3.  The dashboard uses placeholder queries. You will need to update them to query your CSV files. For example, to display the leaderboard, you would select the `participants.csv` file and perform transformations to join it with data from `scores.csv`, `criteria.csv`, and `bonus.csv` using the **Merge** and **Join by field** transformations available in Grafana.

---

## Data Schema

### `participants.csv`
| Column | Type | Description |
|---|---|---|
| `participant_id` | Integer | Unique identifier for each team. |
| `team_name` | String | The name of the team. |
| `category` | String | The track or category the team is competing in. |
| `members` | String | A comma-separated list of team members. |

### `judges.csv`
| Column | Type | Description |
|---|---|---|
| `judge_id` | Integer | Unique identifier for each judge. |
| `judge_name` | String | The name of the judge. |

### `criteria.csv`
| Column | Type | Description |
|---|---|---|
| `criteria_id` | Integer | Unique identifier for each scoring criterion. |
| `criteria_name` | String | The name of the criterion (e.g., "Innovation"). |
| `weightage` | Float | The weight of this criterion in the final score (e.g., 0.30 for 30%). |

### `scores.csv`
| Column | Type | Description |
|---|---|---|
| `score_id` | Integer | Unique identifier for the score entry. |
| `participant_id` | Integer | Foreign key referencing `participants.csv`. |
| `judge_id` | Integer | Foreign key referencing `judges.csv`. |
| `criteria_id` | Integer | Foreign key referencing `criteria.csv`. |
| `score_value` | Integer | The score given (e.g., 1-100). |
| `timestamp` | DateTime | When the score was recorded. |

### `bonus.csv`
| Column | Type | Description |
|---|---|---|
| `event_id` | Integer | Unique identifier for the bonus event. |
| `participant_id` | Integer | Foreign key referencing `participants.csv`. |
| `bonus_type` | String | Reason for the bonus points (e.g., "Pre-event Workshop"). |
| `points` | Integer | The number of bonus points awarded. |
| `timestamp` | DateTime | When the bonus was awarded. |

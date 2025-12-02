# Analytics Schema Documentation

## Root Level Fields

| Field          | Type             | Description                                           |
| -------------- | ---------------- | ----------------------------------------------------- |
| `$schema`      | string (URI)     | JSON Schema version reference for validation          |
| `version`      | string (semver)  | Configuration file version for compatibility tracking |
| `lastUpdated`  | string (ISO8601) | Timestamp of last configuration update                |
| `metrics`      | array            | Array of Metric Objects, định nghĩa tất cả các metric |
| `dashboards`   | array            | Array of Dashboard Objects, cấu hình dashboard        |
| `exportConfig` | object           | Data export configuration                             |

---

## Metric Object

| Field        | Type   | Description                                       |
| ------------ | ------ | ------------------------------------------------- |
| `id`         | string | Unique metric identifier, format: `METRIC_[NAME]` |
| `meta`       | object | Metadata hiển thị & phân loại metric              |
| `collection` | object | Cấu hình thu thập dữ liệu                         |
| `storage`    | object | Cấu hình lưu trữ dữ liệu                          |
| `alerts`     | object | Cấu hình cảnh báo                                 |

### Meta Object

| Field         | Type   | Description                                                                                           |
| ------------- | ------ | ----------------------------------------------------------------------------------------------------- |
| `name`        | string | Tên metric hiển thị                                                                                   |
| `description` | string | Mục đích & cách tính metric                                                                           |
| `category`    | string | `"Engagement"`, `"Retention"`, `"Monetization"`, `"Economy"`, `"Progression"`, `"PvP"`, `"Technical"` |
| `priority`    | string | `"Critical"`, `"High"`, `"Medium"`, `"Low"` (mặc định `"Medium"`)                                     |

---

### Collection Object

| Field         | Type   | Description                                                                                                                                                                            |
| ------------- | ------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `event`       | string | Game event kích hoạt metric                                                                                                                                                            |
| `frequency`   | string | `"RealTime"`, `"Daily"`, `"Hourly"`, `"Weekly"`                                                                                                                                        |
| `dataType`    | string | `"Count"`, `"Duration"`, `"Percentage"`, `"Currency"`, `"Amount"`                                                                                                                      |
| `aggregation` | string | `"UniqueCount"`, `"Sum"`, `"Average"`, `"RetentionCohort"`, `"ConversionRate"`, `"CompletionRate"`, `"ParticipationRate"`, `"CrashRate"`, `"AveragePerUser"`, `"AveragePerPayingUser"` |
| `dimensions`  | array  | Danh sách dimension để phân đoạn dữ liệu (VD: Date, Platform, Region)                                                                                                                  |

---

### Storage Object

| Field           | Type   | Description                                                                    |
| --------------- | ------ | ------------------------------------------------------------------------------ |
| `database`      | string | `"MongoDB"`, `"PostgreSQL"`, `"BigQuery"`, `"Redshift"` (mặc định `"MongoDB"`) |
| `collection`    | string | Tên collection/table                                                           |
| `retentionDays` | number | Số ngày lưu trữ dữ liệu (VD: 30, 90, 180, 365, 730)                            |

---

### Alerts Object

| Field       | Type    | Description                         |
| ----------- | ------- | ----------------------------------- |
| `enabled`   | boolean | Bật/tắt cảnh báo (mặc định `false`) |
| `threshold` | object  | Cấu hình ngưỡng cảnh báo            |

#### Threshold Object

| Field        | Type   | Description                                                                     |
| ------------ | ------ | ------------------------------------------------------------------------------- |
| `type`       | string | `"Percentage"` hoặc `"Absolute"`                                                |
| `value`      | number | Giá trị ngưỡng (VD: -10, 2, 40)                                                 |
| `comparison` | string | `"DayOverDay"`, `"WeekOverWeek"`, `"MonthOverMonth"`, `"Below"`, `"Above"`      |
| `scope`      | string | `"Global"`, `"PerPlatform"`, `"PerStage"`, `"PerSegment"` (mặc định `"Global"`) |

---

## Dashboard Object

| Field             | Type   | Description                                     |
| ----------------- | ------ | ----------------------------------------------- |
| `id`              | string | Unique dashboard ID, format: `DASHBOARD_[NAME]` |
| `name`            | string | Tên hiển thị dashboard                          |
| `metrics`         | array  | Danh sách Metric IDs hiển thị trên dashboard    |
| `refreshInterval` | number | Auto-refresh interval (giây, mặc định 300)      |

---

## Export Config Object

| Field         | Type    | Description                                                         |
| ------------- | ------- | ------------------------------------------------------------------- |
| `enabled`     | boolean | Bật/tắt export dữ liệu (mặc định `true`)                            |
| `format`      | string  | `"CSV"`, `"JSON"`, `"Parquet"`, `"Avro"` (mặc định `"CSV"`)         |
| `schedule`    | string  | `"Hourly"`, `"Daily"`, `"Weekly"`, `"Monthly"` (mặc định `"Daily"`) |
| `destination` | string  | `"S3"`, `"GCS"`, `"Azure"`, `"MongoDB"`, `"SFTP"` (mặc định `"S3"`) |
| `compression` | boolean | Nén dữ liệu xuất (true: gzip, false: không nén, mặc định true)      |

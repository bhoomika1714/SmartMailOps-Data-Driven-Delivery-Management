

### 🔁 1. **Generating Unique Mail Tracking Numbers**

**Problem Statement:** Automatically assign a unique tracking ID to each Mail entry for delivery tracking.

* **Required Transformer(s):**

  * `Sequence Generator` – to generate unique tracking IDs.
  * `Expression` – to format the ID (e.g., `MAIL_<SEQ_NUM>`).

---

### 📍 2. **Route Allocation Based on Destination Address**

**Problem Statement:** Route mail to the correct delivery route based on destination address.

* **Required Transformer(s):**

  * `Router` – to filter based on regions/zones or partial address match.
  * `Lookup` – to fetch `DRoute_id` based on `Dest_add`.
  * `Update Strategy` – to update Mail with assigned route.

---

### 🗃️ 3. **Assigning Employees to Vehicles**

**Problem Statement:** Assign employees to delivery vehicles based on availability and delivery load.

* **Required Transformer(s):**

  * `Lookup` – to find available employees from `Employee` table.
  * `Filter` – to remove unavailable employees.
  * `Update Strategy` – to update the `Vehicle` table with `Emp_id`.

---

### 🧾 4. **Calculating Total Billing Amount**

**Problem Statement:** Compute billing amount using service and weight charge.

* **Required Transformer(s):**

  * `Lookup` – to get `Service_charge` and `Weight_charge_gm` from `Services`.
  * `Expression` – to calculate `amount = Service_charge + (weight * Weight_charge_gm)`.
  * `Aggregator` – to group by `User_id` or `Billing_date` for summary.

---

### 📦 5. **Mailbox Allocation**

**Problem Statement:** Assign the most suitable mailbox based on size requirements and availability.

* **Required Transformer(s):**

  * `Filter` – to exclude unavailable (`Mb_status != 'Available'`) mailboxes.
  * `Sorter` – to sort by size or accessibility.
  * `Rank` – to pick the top N best-suited mailboxes.
  * `Update Strategy` – to assign mailbox to mail.

---

### ⏱️ 6. **Late Delivery Detection**

**Problem Statement:** Identify deliveries that exceeded expected delivery time.

* **Required Transformer(s):**

  * `Expression` – to calculate time difference between `Mail.date/time` and `Delivery_status.Delivery_date/time`.
  * `Filter` – to select only records where `Delivery_time > expected`.

---

### 🔄 7. **Branch-Wise Delivery Report**

**Problem Statement:** Generate delivery status summary grouped by branch.

* **Required Transformer(s):**

  * `Lookup` – to enrich data with `Branch_name`.
  * `Aggregator` – to count delivery statuses (`Delivered`, `Pending`, etc.).
  * `Sorter` – to organize output by branch.

---

### 🔗 8. **Enrich Mail with Sender & Receiver Details**

**Problem Statement:** Attach sender and receiver contact/address info to Mail records.

* **Required Transformer(s):**

  * `Lookup` – twice on `User` table (for sender and receiver IDs).
  * `Expression` – to merge fields into readable format (e.g., full address).

---

### 🔄 9. **Unpivot Mailbox Data for Reporting**

**Problem Statement:** Normalize wide mailbox data into a tall format for analytics (e.g., separate records for cost, size, and location).

* **Required Transformer(s):**

  * `Unpivot` (or manually via multiple `Expression` + `Union`) – to convert wide attributes into row-level data.

---

### 🔄 10. **Reconstruct Mailbox Info Back (Reverse of Unpivot)**

**Problem Statement:** Regroup unpivoted data into original wide format.

* **Required Transformer(s):**

  * `Pivot` or `Unlookup` logic – to combine key-value row data into single-record format.


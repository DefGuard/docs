# High disk usage

Some users may experience unusually high disk activity from the Defguard desktop client, even when the app appears idle. This is almost always caused by SQLite performing full-table scans on large statistics tables.

### **1. Check Your Client Version**

The fix for the original table-scan problem was introduced in **v1.5.2**.

**Steps:**

* Open Defguard Desktop
* Look at the **bottom-left corner** → confirm the version is ≥ **v1.5.2**
* If not, download the latest version: [https://defguard.net/download/](https://defguard.net/download/)

### **2. Check the Database Size**

If the stats purge mechanism is not working correctly or heavy usage generates too much data, the database can grow very large.

**Location of the DB file: `C:\Users<YOUR_USERNAME>\AppData\Roaming\net.defguard\defguard.db`**

**Typical sizes:**

* Normal usage: a few MB
* Problematic: tens or hundreds of MB (e.g., 50–200 MB)

### **3. Temporary Workarounds**

{% hint style="danger" %}
Before trying any fix, **back up your database file**.
{% endhint %}

**Option A — Trim Stats Tables (safe & recommended)**

Install [SQLite](https://sqlite.org/index.html), open the DB, and run:

```sql
delete from location_stats;
delete from tunnel_stats;
```

This immediately reduces the DB size and disk I/O.

**Option B — Remove the Database Entirely (quickest, but requires re-enrollment)**

1. Close Defguard completely
2. Delete the database file: `C:\Users<YOUR_USERNAME>\AppData\Roaming\net.defguard\defguard.db`
3. Re-enroll your device in Defguard (you will need your enrollment link or admin setup)

This is the nuclear option but works reliably.

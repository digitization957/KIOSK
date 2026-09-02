# Kiosk App — MySQL Queries

All queries are read-only `SELECT` statements. No INSERT / UPDATE / DELETE anywhere.
In code they are parameterized (`%s`) — values below are shown as samples only.

---

## 1. Pending vehicles (server.py:63)
Used by `fetch_pending_db()` — today's vehicles still outside the gate.

```sql
SELECT ve.id, ve.vehicle_no, ve.transporter, ve.buyer_name,
       ve.token, ve.without_pcs, ve.manual_pcs, ve.material,
       ve.submitted_at, ve.is_inside, ve.inside_at, ve.reported_on,
       pg.PG_Name AS pg_name
FROM vehicle_entries ve
LEFT JOIN plant_master.tbl_PG pg ON pg.PG_ID = ve.pg_id
WHERE ve.is_inside = 0 AND DATE(ve.submitted_at) = CURDATE()
ORDER BY ve.submitted_at DESC;
```

---

## 2. Vehicle by ID (server.py:168)

```sql
SELECT ve.*, pg.PG_Name AS pg_name
FROM vehicle_entries ve
LEFT JOIN plant_master.tbl_PG pg ON pg.PG_ID = ve.pg_id
WHERE ve.id = 123;
```

---

## 3. Vehicle by number (server.py:176)

```sql
SELECT ve.*, pg.PG_Name AS pg_name
FROM vehicle_entries ve
LEFT JOIN plant_master.tbl_PG pg ON pg.PG_ID = ve.pg_id
WHERE ve.vehicle_no = 'MH12AB1234'
ORDER BY ve.submitted_at DESC;
```

---

## 4. All vehicles today (server.py:186)

```sql
SELECT ve.*, pg.PG_Name AS pg_name
FROM vehicle_entries ve
LEFT JOIN plant_master.tbl_PG pg ON pg.PG_ID = ve.pg_id
WHERE DATE(ve.submitted_at) = CURDATE()
ORDER BY ve.submitted_at DESC;
```

---

## 5. Relay pending vehicles (relay_server.py:33)
Lighter column set, used by the relay server endpoint `/api/vehicles`.

```sql
SELECT ve.id, ve.vehicle_no, ve.transporter, ve.without_pcs, pg.PG_Name AS pg_name
FROM vehicle_entries ve
LEFT JOIN plant_master.tbl_PG pg ON pg.PG_ID = ve.pg_id
WHERE ve.is_inside = 0 AND DATE(ve.submitted_at) = CURDATE()
ORDER BY ve.submitted_at DESC;
```

---

## Tables used
| Table | Purpose |
|---|---|
| `vehicle_entries` | Main vehicle entry records |
| `plant_master.tbl_PG` | Plant/gate master, gives `PG_Name` |

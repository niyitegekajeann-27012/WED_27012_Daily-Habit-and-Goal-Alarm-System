# **📊Daily\_Habit\_and\_Goal\_Alarm\_System**

<br>

 **NAME**:   Pacifique HARERIMANA
 <br>
 **ID**:        26937
 <br>
 **CONCENTRATION**:   Software Engineering

---
<br>

<br>


## The documents in the main branch (Phase 2 to Phase 7) outline the complete system development of a personal habit and goal alarm system for tracking user routines, setting alarms, and storing logs.

---

<br>
<br>

# PHASE 2: 💼 *Business Process Modeling*

⚡ ***Scope***: Creating a personal habit tracking and goal management platform.

🎯 ***Objectives***: Help users maintain routines, track goals, receive timely alarms, and store progress securely.

🧩 ***Entities***: User, Habit, Goal, Alarm, Habit\_Log, Goal\_Status

📈 ***MIS Significance***: Enables personal productivity and wellness tracking using database automation and reporting.

<br>

![ChatGPT Image May 22, 2025, 06_34_36 PM](https://github.com/user-attachments/assets/d5df2d3e-8911-43c1-a647-bdf1c01555f4)


---

<br>
<br>

#  PHASE 3: ⚙️ *Logical Model Design*

🗃️ ***Entities***: User, Habit, Goal, Alarm, Habit\_Log, Goal\_Status

🔗 ***Relationships***:

* One User to many Habits and Goals
* One Habit to one Alarm
* One Habit to many Habit\_Log entries
* One Goal to many Goal\_Status updates

🎯 ***Goal***: Provide a normalized, scalable, and relational database structure for user behavior tracking and automation.

<br>

![ChatGPT Image May 22, 2025, 07_58_09 PM](https://github.com/user-attachments/assets/6d58f591-735a-49a2-8d44-eb721137b574)

---
<br>
<br>

# PHASE 4: 🧰 *Creating the Database (Oracle SQL Developer)*

## 🛠️ Creation of Project Schema:

```sql
CREATE USER Thur_26937_Pacifique_Daily_habit_alarmS_db IDENTIFIED BY Pacifique;
GRANT CONNECT, RESOURCE, DBA TO Thur_26937_Pacifique_Daily_habit_alarmS_db;
ALTER USER Thur_26937_Pacifique_Daily_habit_alarmS_db ACCOUNT UNLOCK;
```

## 🔧 SQL Developer Configuration:

* Username: Thur\_26937\_Pacifique\_Daily\_habit\_alarmS\_db
* Password: Pacifique
* Host: localhost
* Port: 1521
* Service Name: orcll

<br>

![connection](https://github.com/user-attachments/assets/bce092ed-3dc0-434d-b3b8-9aa3955bf7fc)



---

<br>
<br>


# PHASE 5: 🧱 *Table Implementation and Data Insertion*

## 🧾 Table Creation

Tables implemented:

* users
* habit
* goal
* alarm
* habit\_log
* goal\_status

All constraints applied: PKs, FKs, NOT NULL, UNIQUE, CHECK.

## ✍️ Sample Data (7 rows per table)

Inserted realistic data for tracking workout, reading, and goal progress. Demonstrates user interaction and system automation.

![Tables Screenshot](Phase%205%20\(Tables%20screenshot\).png)

---

# PHASE 6: 🧮 *Database Interaction and Transactions*

## ✏️ DML/DDL Examples

Performed: INSERT, UPDATE, DELETE, CREATE, ALTER, DROP

## 🛎️ Stored Procedure

```sql
CREATE OR REPLACE PROCEDURE get_habit_logs (p_user_id IN NUMBER) IS
BEGIN
  FOR rec IN (
    SELECT h.title, l.log_date, l.status
    FROM habit h JOIN habit_log l ON h.habit_id = l.habit_id
    WHERE h.user_id = p_user_id
  ) LOOP
    DBMS_OUTPUT.PUT_LINE('Habit: ' || rec.title || ' | Date: ' || rec.log_date || ' | Status: ' || rec.status);
  END LOOP;
END;
```

## 🧠 Function

```sql
CREATE OR REPLACE FUNCTION get_goal_completion (p_goal_id IN NUMBER) RETURN NUMBER IS
  v_percent NUMBER;
BEGIN
  SELECT completion_percentage INTO v_percent
  FROM goal_status
  WHERE goal_id = p_goal_id
  ORDER BY update_date DESC FETCH FIRST 1 ROWS ONLY;
  RETURN v_percent;
EXCEPTION
  WHEN NO_DATA_FOUND THEN RETURN 0;
END;
```

## 🔄 Cursor with Exception Handling

```sql
DECLARE
  CURSOR cur_habits IS SELECT title FROM habit WHERE user_id = 1;
  v_title habit.title%TYPE;
BEGIN
  OPEN cur_habits;
  LOOP
    FETCH cur_habits INTO v_title;
    EXIT WHEN cur_habits%NOTFOUND;
    DBMS_OUTPUT.PUT_LINE('Habit: ' || v_title);
  END LOOP;
  CLOSE cur_habits;
EXCEPTION WHEN OTHERS THEN
  DBMS_OUTPUT.PUT_LINE('Error: ' || SQLERRM);
END;
```

## 📦 Package

Includes procedure to log today's habit and function to count active habits.

---

# PHASE 7: 🕵️ *Advanced Database Programming and Auditing*

## 🧾 Problem Statement

To prevent users from modifying data on weekdays or public holidays, and to log all critical actions.

## 📅 Holiday Table

```sql
CREATE TABLE public_holidays (
  holiday_date DATE PRIMARY KEY,
  description VARCHAR2(100)
);
```

## 🚫 Restriction Trigger

```sql
CREATE OR REPLACE TRIGGER restrict_weekday_and_holiday_dml
BEFORE INSERT OR UPDATE OR DELETE ON habit
FOR EACH ROW
DECLARE
  v_day VARCHAR2(10);
  v_holiday NUMBER;
BEGIN
  SELECT TO_CHAR(SYSDATE, 'DY') INTO v_day FROM dual;
  SELECT COUNT(*) INTO v_holiday FROM public_holidays WHERE holiday_date = TRUNC(SYSDATE);
  IF v_day IN ('MON', 'TUE', 'WED', 'THU', 'FRI') OR v_holiday > 0 THEN
    RAISE_APPLICATION_ERROR(-20001, 'DML is blocked on weekdays and public holidays!');
  END IF;
END;
```

## 📋 Auditing Table and Trigger

```sql
CREATE TABLE audit_log (
  audit_id NUMBER GENERATED BY DEFAULT AS IDENTITY PRIMARY KEY,
  user_id NUMBER,
  action_date DATE DEFAULT SYSDATE,
  operation VARCHAR2(50),
  status VARCHAR2(20)
);

CREATE OR REPLACE TRIGGER audit_habit_dml
AFTER INSERT OR UPDATE OR DELETE ON habit
FOR EACH ROW
BEGIN
  INSERT INTO audit_log (user_id, operation, status)
  VALUES (1, 'MODIFY HABIT', 'allowed');
END;
```

## 🧾 Result Summary

* 🚫 Security logic implemented with triggers
* 📅 Public holiday awareness built into DML restrictions
* 🕵️ Full audit log of changes to sensitive data

---

📘 Ready for [Phase 8 Documentation and GitHub Report](../phase_viii_documentation).

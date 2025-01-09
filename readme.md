1. Users collection

db.users.insertMany(
[
{ "_id": 1, "name": "kesavan", "email": "kesavan@gmail.com" },
{ "_id": 2, "name": "harish", "email": "harish@gmail.com" },
{ "_id": 3, "name": "kavya", "email": "kavya@hotmail.com" }
])
2. Codekata collection:

db.codekata.insertMany(
[
  { "_id": 1, "user_id": 1, "problems_solved": 120 },
  { "_id": 2, "user_id": 2, "problems_solved": 80 },
  { "_id": 3, "user_id": 3, "problems_solved": 150 }
])
3. Attendance collection:

db.attendance.insertMany(
[
  { "_id": 1, "user_id": 1, "date": "2020-10-15", "status": "Present" },
  { "_id": 2, "user_id": 2, "date": "2020-10-16", "status": "Absent" },
  { "_id": 3, "user_id": 3, "date": "2020-10-17", "status": "Absent" }
])
4. Topics collection:

db.topics.insertMany(
[
  { "_id": 1, "name": "JavaScript", "date": "2020-10-05" },
  { "_id": 2, "name": "React", "date": "2020-10-15" }
])
5. Task collection:

db.tasks.insertMany(
[
  { "_id": 1, "name": "JavaScript", "date": "2020-10-05" },
  { "_id": 2, "name": "React", "date": "2020-10-15" }
])
6. Company_drives collection:

db.company_drives.insertMany(
[
  { "_id": 1, "company_name": "Google", "date": "2020-10-20", "students_appeared": [1, 2] },
  { "_id": 2, "company_name": "Amazon", "date": "2020-10-25", "students_appeared": [2, 3] }
])
7. Mentors collection:

db.mentors.insertMany(
[
  { "_id": 1, "name": "Mentor A", "mentees_count": 10 },
  { "_id": 2, "name": "Mentor B", "mentees_count": 20 }
])

### QUERIES


1. Find all the topics and tasks which are taught in the month of October
SELECT t.topic_name, ta.task_name
FROM topics t
JOIN tasks ta ON t.id = ta.topic_id
WHERE MONTH(t.date) = 10; 
-- This query retrieves all topics and tasks taught in the month of October. 


2. Find all the company drives which appeared between 15th Oct 2020 and 31st Oct 2020
SELECT *
FROM company_drives
WHERE drive_date BETWEEN '2020-10-15' AND '2020-10-31';
-- This query retrieves all company drives held between 15th Oct 2020 and 31st Oct 2020.


3. Find all the company drives and students who appeared for the placement
SELECT cd.drive_name, u.name AS student_name
FROM company_drives cd
JOIN users u ON cd.id = u.drive_id
WHERE u.status = 'Appeared for Placement';
-- This query retrieves all company drives along with students who appeared for the placement.


4. Find the number of problems solved by the user in Codekata
SELECT u.name, COUNT(c.problem_id) AS problems_solved
FROM users u
JOIN codekata c ON u.id = c.user_id
WHERE c.status = 'Solved'
GROUP BY u.id;
-- This query calculates the number of problems solved by each user in Codekata.


5. Find all the mentors who have more than 15 mentees
SELECT m.name AS mentor_name
FROM mentors m
JOIN mentees me ON m.id = me.mentor_id
GROUP BY m.id
HAVING COUNT(me.id) > 15;
-- This query retrieves mentors who have more than 15 mentees.


6. Find the number of users who were absent and did not submit the task between 15th Oct 2020 and 31st Oct 2020
SELECT COUNT(DISTINCT u.id) AS absent_and_not_submitted
FROM users u
JOIN attendance a ON u.id = a.user_id
JOIN tasks ta ON u.id = ta.user_id
WHERE a.status = 'Absent' 
AND ta.submission_status = 'Not Submitted'
AND a.date BETWEEN '2020-10-15' AND '2020-10-31';
-- This query retrieves the number of users who were absent and did not submit th
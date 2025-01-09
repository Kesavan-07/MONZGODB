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
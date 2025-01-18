// MONGO DB TASK

// Design database for Zen class programme

// Step 1: Create the Zen class database
use zenClass;

// Step 2: Create and populate collections

// 1. Users Collection
// This collection contains details of students in the batch.
db.users.insertMany([
  { "_id": 1, "name": "Kesavan", "email": "Kesavan@example.com" },
  { "_id": 2, "name": "Harish", "email": "Harish@example.com" },
  { "_id": 3, "name": "Ilakkya", "email": "Ilakkya@example.com" }
]);

// 2. Codekata Collection
// This collection tracks the number of problems solved by each student.
db.codekata.insertMany([
  { "_id": 1, "user_id": 1, "problems_solved": 120 },
  { "_id": 2, "user_id": 2, "problems_solved": 80 },
  { "_id": 3, "user_id": 3, "problems_solved": 150 }
]);

// 3. Attendance Collection
// This collection records attendance of students for specific dates.
db.attendance.insertMany([
  { "_id": 1, "user_id": 1, "date": ISODate("2020-10-15"), "status": "Present" },
  { "_id": 2, "user_id": 2, "date": ISODate("2020-10-16"), "status": "Absent" },
  { "_id": 3, "user_id": 3, "date": ISODate("2020-10-17"), "status": "Absent" }
]);

// 4. Topics Collection
// This collection lists the topics covered on specific dates.
db.topics.insertMany([
  { "_id": 1, "name": "JavaScript", "date": ISODate("2020-10-05") },
  { "_id": 2, "name": "React", "date": ISODate("2020-10-15") }
]);

// 5. Tasks Collection
// This collection stores task details and their submission statuses.
db.tasks.insertMany([
  { "_id": 1, "user_id": 1, "name": "JavaScript", "date": ISODate("2020-10-05"), "task_status": "Submitted" },
  { "_id": 2, "user_id": 2, "name": "React", "date": ISODate("2020-10-15"), "task_status": "Not Submitted" }
]);

// 6. Company Drives Collection
// This collection tracks placement drives and participating students.
db.company_drives.insertMany([
  { "_id": 1, "company_name": "Google", "date": ISODate("2020-10-20"), "students_appeared": [1, 2] },
  { "_id": 2, "company_name": "Amazon", "date": ISODate("2020-10-25"), "students_appeared": [2, 3] }
]);

// 7. Mentors Collection
// This collection maintains details about mentors and their mentee counts.
db.mentors.insertMany([
  { "_id": 1, "name": "Mentor A", "mentees_count": 10 },
  { "_id": 2, "name": "Mentor B", "mentees_count": 20 }
]);

// QUERIES

// Query 1: Find all the topics and tasks taught in the month of October
// Retrieve topics taught in October
db.topics.find({ date: { $gte: ISODate("2020-10-01"), $lte: ISODate("2020-10-31") } });

// Retrieve tasks assigned in October
db.tasks.find({ date: { $gte: ISODate("2020-10-01"), $lte: ISODate("2020-10-31") } });

// Query 2: Find all the company drives which appeared between 15-Oct-2020 and 31-Oct-2020
// Retrieve company drives held in the specified date range
db.company_drives.find({ date: { $gte: ISODate("2020-10-15"), $lte: ISODate("2020-10-31") } });

// Query 3: Find all the company drives and students who appeared for the placement
// Retrieve all company drives
db.company_drives.find();

// Retrieve students who appeared for placements
db.users.find({ _id: { $in: db.company_drives.distinct("students_appeared") } });

// Query 4: Find the number of problems solved by each user in Codekata
// Use aggregation to combine user details with their problems solved
db.codekata.aggregate([
  {
    $lookup: {
      from: "users",
      localField: "user_id",
      foreignField: "_id",
      as: "user_details"
    }
  },
  {
    $project: {
      user_name: { $arrayElemAt: ["$user_details.name", 0] },
      email: { $arrayElemAt: ["$user_details.email", 0] },
      problems_solved: 1
    }
  }
]);

// Query 5: Find all the mentors who have a mentee count of more than 15
// Retrieve mentors with mentees count greater than 15
db.mentors.find({ mentees_count: { $gt: 15 } });

// Query 6: Find the number of users who are absent and tasks not submitted between 15-Oct-2020 and 31-Oct-2020
// Use aggregation to find absent users and check their task submission status
db.attendance.aggregate([
  {
    $match: {
      date: { $gte: ISODate("2020-10-15"), $lte: ISODate("2020-10-31") },
      status: "Absent"
    }
  },
  {
    $lookup: {
      from: "tasks",
      localField: "user_id",
      foreignField: "user_id",
      as: "tasks"
    }
  },
  {
    $project: {
      user_id: 1,
      absent_on: "$date",
      tasks_pending: {
        $filter: {
          input: "$tasks",
          as: "task",
          cond: { $ne: ["$$task.task_status", "Submitted"] }
        }
      }
    }
  }
]);
///

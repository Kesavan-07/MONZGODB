# MONGO DB TASK

## Design database for Zen class programme

### Creating zen class database:

  
```
  use zenClass 
```

## 1.) users collection 

### Assume the batch has around 3 students.
  
  ```
  db.users.insertMany(
  [
  { "_id": 1, "name": "Kesavan", "email": "Kesavan@example.com" },
  { "_id": 2, "name": "Harish", "email": "Harish@example.com" },
  { "_id": 3, "name": "Ilakkya", "email": "Ilakkya@example.com" }
  ])
  ```

 ## 2.)Codekata collection:

```
db.codekata.insertMany(
[
  { "_id": 1, "user_id": 1, "problems_solved": 120 },
  { "_id": 2, "user_id": 2, "problems_solved": 80 },
  { "_id": 3, "user_id": 3, "problems_solved": 150 }
])
```

## 3.)Attendance collection:

```
db.attendance.insertMany(
[
  { "_id": 1, "user_id": 1, "date": "2020-10-15", "status": "Present" },
  { "_id": 2, "user_id": 2, "date": "2020-10-16", "status": "Absent" },
  { "_id": 3, "user_id": 3, "date": "2020-10-17", "status": "Absent" }
])
```

## 4.) Topics collection:

```
db.topics.insertMany(
[
  { "_id": 1, "name": "JavaScript", "date": "2020-10-05" },
  { "_id": 2, "name": "React", "date": "2020-10-15" }
])
```

## 5.) Task collection:

```
db.tasks.insertMany(
[
  { "_id": 1, "name": "JavaScript", "date": "2020-10-05" },
  { "_id": 2, "name": "React", "date": "2020-10-15" }
])
```

## 6.) Company_drives collection:

```
db.company_drives.insertMany(
[
  { "_id": 1, "company_name": "Google", "date": "2020-10-20", "students_appeared": [1, 2] },
  { "_id": 2, "company_name": "Amazon", "date": "2020-10-25", "students_appeared": [2, 3] }
])
```

## 7.) Mentors collection:

```
db.mentors.insertMany(
[
  { "_id": 1, "name": "Mentor A", "mentees_count": 10 },
  { "_id": 2, "name": "Mentor B", "mentees_count": 20 }
])
```

# QUERIES

## 1.Find all the topics and tasks taught in the month of October

- To find all Topic
```

db.topics.find();

```
- To find task in October
```
db.tasks.find({ date: { $gte: "2020-10-01", $lte: "2020-10-31" } })

```



## 2. Find all the company drives which appeared between 15-Oct-2020 and 31-Oct-2020
```
db.company_drives.find({ date: { $gte: "2020-10-15", $lte: "2020-10-31" } })


```

## 3. Find all the company drives and students who appeared for the placement

- To find all company drives
```
db.company_drives.find();

```
- To find student who appeared for placement
```
db.users.find({ _id: { $in: [1, 2, 3] } });

```

## 4.Find the number of problems solved by each user in Codekata

```
db.codekata.aggregate([
  {
    $lookup: {
      from: "users",                
      localField: "user_id",        
      foreignField: "_id",          
      as: "user_details"               }
  },
  {
    $project: {
      user_name: { $arrayElemAt: ["$user_details.name", 0] }, 
      email: { $arrayElemAt: ["$user_details.email", 0] },    
      problems_solved: 1                                      
  }
]);


```

## 5.  Find all the mentors who have a mentee count of more than 15

```
db.mentors.find({
  mentees_count: { $gt: 15 }
});

```

## 6.Find the number of users who are absent and tasks not submitted between 15-Oct-2020 and 31-Oct-2020

```
db.attendance.aggregate([
  {$match: {date: { $gte: ISODate("2020-10-15"), $lte: ISODate("2020-10-31") },status: "Absent"}},
  {
    $lookup: {
      from: "tasks",
      localField: "user_id",
      foreignField: "user_id",
      as: "task_submission"
    }
  },
  {
    $match: {
      "task_submission.task_status": { $ne: "Submitted" }  
    }
  },
  {
    $project: {
      user_id: 1
    }
  }
]);


```

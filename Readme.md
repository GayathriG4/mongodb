use zen class program
switched to db zen

db.createCollection("user");

db.users.insertOne({
  "name": "John Doe",
  "email": "john.doe@example.com",
  "mentee_mentor_id": ObjectId("507f191e810c19729de860ea"),
  "attendance": ["2024-10-01", "2024-10-02"],
  "tasks": [ObjectId("507f191e810c19729de860eb")]
});

db.createCollection("codeketa");

db.codeketa.insertOne({
  "_id": ObjectId,
  "user_id": ObjectId, // Refers to users._id
  "problems_solved": Number
}
);

db.createCollection("attendence");

db.attendence.insertOne({
  "_id": ObjectId,
  "user_id": ObjectId, // Refers to users._id
  "date": Date,
  "is_present": Boolean
}
);

db.createCollection("topic");

db.topic.insertOne({
  "_id": ObjectId,
  "name": String,
  "date": Date // Date of the topic discussion
}
);

db.createCollection("task");

db.task.insertOne({
  "_id": ObjectId,
  "topic_id": ObjectId, // Refers to topics._id
  "name": String,
  "due_date": Date
}
);

db.createCollection("company drives");


db.companydrives.insertOne({
    "_id": ObjectId,
    "company_name": String,
    "date": Date,
    "students_applied": [ObjectId] // Refers to users._id
  }
  );
  
  db.createCollection("mentors");


  db.mentors.insertOne({
    "_id": ObjectId,
    "name": String,
    "mentees": [ObjectId] // Refers to users._id
  }
  );


  db.topics.aggregate([
    {
      $match: {
        date: {
          $gte: new ISODate("2024-10-01T00:00:00Z"),
          $lte: new ISODate("2024-10-31T23:59:59Z")
        }
      }
    },
    {
      $lookup: {
        from: "tasks",
        localField: "_id",
        foreignField: "topic_id",
        as: "tasks"
      }
    }
  ]);


  Queries:
  
  // Find all the topics and tasks which are taught in October

  db.topics.find({
    date: { $gte: ISODate("2020-10-01"), $lt: ISODate("2020-11-01") }
  });
  
  db.tasks.find({
    date: { $gte: ISODate("2020-10-01"), $lt: ISODate("2020-11-01") }
  });

//   Find all the company drives which appeared between 15 oct-2020 and 31-oct-2020
  db.company_drives.find({
    drive_date: { $gte: ISODate("2020-10-15"), $lte: ISODate("2020-10-31") }
  });

//   Find all the company drives and students who are appeared for the placement.

  db.company_drives.aggregate([
    {
      $lookup: {
        from: "users",
        localField: "students",
        foreignField: "_id",
        as: "appeared_students"
      }
    }
  ]);


//   Find the number of problems solved by the user in codekata


  db.codekata.aggregate([
    {
      $group: {
        _id: "$user_id",
        total_problems_solved: { $sum: "$problems_solved" }
      }
    }
  ]);
// Find all the mentors with who has the mentee's count more than 15

  db.mentors.find({
    $where: "this.mentees.length > 15"
  });
//   Find the number of users who are absent and task is not submitted  between 15 oct-2020 and 31-oct-2020

  db.users.aggregate([
    {
      $match: {
        "attendance.date": { $gte: ISODate("2020-10-15"), $lte: ISODate("2020-10-31") },
        "attendance.status": "absent",
        "tasks_submitted.submitted": false
      }
    },
    {
      $count: "absent_and_not_submitted"
    }
  ]);
  
  
  
   

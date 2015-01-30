---
layout: post
title: "Tealeaf_Course2_Lesson1_Quiz"
date: 2015-01-30 08:13:42 +0000
comments: true
categories: 
---

1. Why do they call it a relational database?

2. What is SQL?

3. There are two predominant views into a relational database. What are they, and how are they different?

4. In a table, what do we call the column that serves as the main identifier for a row of data? We're looking for the general database term, not the column name.

5. What is a foreign key, and how is it used?

6. At a high level, describe the ActiveRecord pattern. This has nothing to do with Rails, but the actual pattern that ActiveRecord uses to perform its ORM duties.

7. If there's an ActiveRecord model called "CrazyMonkey", what should the table name be?

8. If I'm building a 1:M association between Project and Issue, what will the model associations and foreign key be?

9. Given this code

```
class Zoo < ActiveRecord::Base
  has_many :animals
end
```
  - What do you expect the other model to be and what does database schema look like?
  - What are the methods that are now available to a zoo to call related to animals?
  - How do I create an animal called "jumpster" in a zoo called "San Diego Zoo"?

10. What is mass assignment? What's the non-mass assignment way of setting values?

11. What does this code do? `Animal.first`

12. If I have a table called "animals" with columns called "name", and a model called `Animal`, how do I instantiate an animal object with name set to "Joe". Which methods makes sure it saves to the database?

13. How does a M:M association work at the database level?

14. What are the two ways to support a M:M association at the ActiveRecord model level? Pros and cons of each approach?

15. Suppose we have a User model and a Group model, and we have a M:M association all set up. How do we associate the two?
# Observer Design Pattern

**Introduction: the Observer Design Pattern, Courses, and Students at Northeastern University.
**

As ALIGN graduate students at Northeastern University, I am sure we are all familiar with the Canvas Dashboard - our Learning Management System’s Dashboard.  

The Canvas Dashboard is the frontend access that connects us to the courses we subscribe to during any given semester; and although we are expected to register for at least one course or at most three courses per semester, we most likely don’t stay conscious that there are at least “318 undergraduate majors, 444 graduate programs, and 36 PhD disciplines” [2] most likely also being managed through Canvas, and likely subscribed to by over 27,000 students located at the different global campuses of the university.

So, the question begs… How do we get information only about the courses we register for and not bombarded by irrelevant notifications outside our choice of courses or program?  

![Screenshot 2022-12-06 at 08 01 28](https://user-images.githubusercontent.com/46625599/205961668-34c43970-ae61-4c50-8de7-2e7cd9e8df9f.png)

**Jordan, Yuki, Courses, and the Observer Design Pattern on Canvas.
**
For example, Jordan is registered for CS5004 and CS5800 this semester, while Yuki is registered for CS5004 and CS5008. But Jordan does not have access to CS5008 contents, neither does Yuki have access to CS5800 contents.  And I am sure neither of them get course related notifications on these courses.  

Why? Because Canvas only updates students with their course specific information.  So, if we are to check Jordan and Yuki’s respective Canvas Dashboards, we are sure that we will only see information relevant to the courses they have each subscribed to.  On one part, there would exist  general information shared by both students on the same course, for example:
•	General course information in the form of course syllabus, Instructor information etc.
•	Course contents in the form of modules, assignments, labs, projects
•	And more.
However, there are also those information which are peculiar to each student based on their respective interactions with their courses on Canvas. For example: Task grades and grade point average updates.

![Screenshot 2022-12-06 at 08 05 56](https://user-images.githubusercontent.com/46625599/205962512-abcc104f-a171-4bcb-981e-06ed2d542936.png)


**Information Dissemination in Software Design.
**

What I just described is not peculiar to Jordan and Yuki alone as over 27,000 students in Northeastern University depends on receiving general and specific information updates from Canvas based on their interactions with their respective campuses, programs of study, and courses.


# Course Example:
To show how Observer Pattern works, we use the course example.
The problem is that there are different kinds of students enrolled in one course. What we want is that when the information of the course changes, all students enrolled should be informed automatically.

# Design Diagram
Here is our design diagram.
<img width="1037" alt="Screen Shot 2022-12-05 at 7 56 00 PM" src="https://user-images.githubusercontent.com/113145079/205822354-c3499222-9c6f-4978-978b-4f54ec462008.png">
 
There are many ways to implement the Observer pattern but most of them include Subject and Observer interface. So here we also use these two interfaces. 

# DisplayElement interface
In addition, we use the DisplayElement interface to make sure that every time students get updated course information, the message will be shown.
  ```
public interface DisplayElement {
  public void display();
}
  ```

# Subject Interface
In the Subject interface, there are three main methods to manage its observers, which are registerObserver, removeObserber, notifyObservers. 
  ```
public interface Subject {
  public void registerObserver(Observer o);
  public void removeObserver(Observer o);
  public void notifyObservers();
}
  ```

# CourseInformation Class
The class that implements the Subject class is CourseInformation. In this class, we have three fields related to the course, which are professor name, assignment number and week number. 
  ```
public class CourseInformation implements Subject {
  private List<Observer> observers;//used to hold observers
  private String professorName;
  private int assignmentNumber;
  private int weekNumber;
 }
  ```

In addition, there is a list to hold observers. Except for those three override methods, there are 5 methods. 
The connection between some of them is that we use setInformation method to change information of course, and then the informationChanged method will be called, then the notifyObservers method will be called. In the notifyObservers method, we us¬¬e the update method to pass updated information of course to all its observers.
  ```
  public void registerObserver(Observer o) {
    observers.add(o);
  }
  public void removeObserver(Observer o) {
    observers.remove(o);
  }
  public void notifyObservers() {
    for (Observer observer : observers) {
      observer.update(professorName, assignmentNumber, weekNumber);
    }
  }
  public void informationChanged() {
    notifyObservers();
  }
  public void setInformation(String professorName, int assignmentNumber, int weekNumber) {
    this.professorName = professorName;
    this.assignmentNumber = assignmentNumber;
    this.weekNumber = weekNumber;
    informationChanged();
  }
  ```
# Observer Interface
Then we talk about Observer interface, there is only one method in this interface, and every class that implements this interface must implement this method. So, every observer class will have this method. As we mentioned before, this method is used to pass updated information of course to observers.
  ```
public interface Observer {
  public void update(String professorName, int assignmentNumber, int weekNumber);
}
  ```
Now let us go ahead with three concrete classes that implement the Observer interface, which represent three different kinds of students, general students, auditors and teaching assistants.
To make this example as simple as possible, so that the Observer pattern can be understood by audiences easily, the content of these three classes is the same. So, we only explain one of them.
 
# GeneralStudent Class
We choose the GeneralStudent class as the example. In this class, we have three fields related to the course, the professor name, the assignment number and the week number. In addition, we also include the courseInformation as one field, it is because that it may be used by this class, such as removing observers. 
  ```
public class GeneralStudent implements Observer, DisplayElement{
  private String professorName;
  private int assignmentNumber;
  private int weekNumber;
  private CourseInformation courseInformation;
 }
  ```

Here we only have two simple methods in this class. One of them is updated, which is used to update its field. At the end of this method, we call display method, so we can know immediately that the student is notified when the course information change.
  ```
public void update(String professorName, int assignmentNumber, int weekNumber) {
    this.professorName = professorName;
    this.assignmentNumber = assignmentNumber;
    this.weekNumber = weekNumber;
    display();
  }
 public void display() {
    System.out.println("Inform the general student of updates\n" +
        "professorName " + professorName + "\n" +
        "assignmentNumber " + assignmentNumber + "\n" +
        "weekNumber " + weekNumber + "\n");
  }
  ```
 
# CourseCenter Class
Finally, we have the CourseCenter class, which is the driver class. In this class, we create an object of CourseInformation class, named courseInformation.
We also create three different kinds of student objects, named general student, auditor, and teaching assistant. Then we change the course information twice to see what will happen. Then we remove auditors from the observer list and change the course information again to see what will happen.
 ```
 public class CourseCenter {
  public static void main(String[] args) {
    CourseInformation courseInformation = new CourseInformation();

    GeneralStudent generalStudent = new GeneralStudent(courseInformation);
    Auditor auditor = new Auditor(courseInformation);
    TeachingAssistant teachingAssistant = new TeachingAssistant(courseInformation);

    System.out.println("First Change!");
    courseInformation.setInformation("Joy", 8, 11);
    System.out.println("Second Change!");
    courseInformation.setInformation("Tom", 7, 12);

    System.out.println("Pay Attention! The auditor will be removed!\n");
    courseInformation.removeObserver(auditor);

    System.out.println("Third Change!");
    courseInformation.setInformation("Jim", 9, 5);
  }
}
  ```

So now we solve the problem. Thanks to the Observer Pattern.

import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.Scanner;

interface Observer {
    void update(String message);
}

class Subject {
    private List<Observer> observers = new ArrayList<>();

    public void addObserver(Observer observer) {
        observers.add(observer);
    }

    public void removeObserver(Observer observer) {
        observers.remove(observer);
    }

    public void notifyObservers(String message) {
        for (Observer observer : observers) {
            observer.update(message);
        }
    }
}

class Employee implements Observer {
    private String name;

    public Employee(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }

    public void sendMessage(Employee recipient, String message) {
        System.out.println("Message from " + name + " to " + recipient.getName() + ": " + message);
    }

    public void sendComplaint(String message) {
        System.out.println("Complaint from " + name + ": " + message);
    }

    @Override
    public void update(String message) {
        System.out.println("Notification for " + name + ": " + message);
    }
}

class Teacher extends Employee {
    public Teacher(String name) {
        super(name);
    }

    public void putMarks(Course course, Student student, int marks) {
        course.putMarks(student, marks);
        course.notifyObservers("Marks added by " + getName() + " for " + student.getName() + " in " + course.getCourseName() + ": " + marks);
    }
}

class Student {
    private String name;
    private int studentId;

    public Student(String name, int studentId) {
        this.name = name;
        this.studentId = studentId;
    }

    public String getName() {
        return name;
    }

    public int getStudentId() {
        return studentId;
    }
}

class Course extends Subject {
    private String courseName;
    private List<Student> enrolledStudents;
    private Map<Student, Integer> studentMarks;

    public Course(String courseName) {
        this.courseName = courseName;
        this.enrolledStudents = new ArrayList<>();
        this.studentMarks = new HashMap<>();
    }

    public void enrollStudent(Student student) {
        enrolledStudents.add(student);
    }

    public void putMarks(Student student, Integer marks) {
        if (enrolledStudents.contains(student)) {
            studentMarks.put(student, marks);
            System.out.println("Marks added for " + student.getName() + " in " + courseName + ": " + marks);

            notifyObservers("New marks added for " + student.getName() + " in " + courseName + ": " + marks);
        } else {
            System.out.println("Student " + student.getName() + " is not enrolled in " + courseName);
        }
    }

    public void viewStudents() {
        System.out.println("Enrolled students in " + courseName + ":");
        for (Student student : enrolledStudents) {
            System.out.println(student.getName() + " (ID: " + student.getStudentId() + ")");
        }
    }

    public String getCourseName() {
        return courseName;
    }
}

class UserFactory {
    private List<Student> students;
    private List<GraduateStudent> graduateStudents;
    private List<Employee> employees;
    private List<Teacher> teachers;
    private List<Manager> managers;

    public UserFactory() {
        this.students = new ArrayList<>();
        this.graduateStudents = new ArrayList<>();
        this.employees = new ArrayList<>();
        this.teachers = new ArrayList<>();
        this.managers = new ArrayList<>();
    }

    public Student createStudent(String name, int studentId) {
        Student student = new Student(name, studentId);
        students.add(student);
        return student;
    }

    public GraduateStudent createGraduateStudent(String name, int studentId, String thesisTopic) {
        GraduateStudent graduateStudent = new GraduateStudent(name, studentId, thesisTopic);
        graduateStudents.add(graduateStudent);
        return graduateStudent;
    }

    public Employee createEmployee(String name) {
        Employee employee = new Employee(name);
        employees.add(employee);
        return employee;
    }

    public Teacher createTeacher(String name) {
        Teacher teacher = new Teacher(name);
        teachers.add(teacher);
        return teacher;
    }

    public Manager createManager(String name) {
        Manager manager = new Manager(name);
        managers.add(manager);
        return manager;
    }
}

class GraduateStudent extends Student {
    private String thesisTopic;

    public GraduateStudent(String name, int studentId, String thesisTopic) {
        super(name, studentId);
        this.thesisTopic = thesisTopic;
    }

    public String getThesisTopic() {
        return thesisTopic;
    }
}

class Manager extends Employee {
    public Manager(String name) {
        super(name);
    }
}

public class SchoolManagement {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);

        System.out.print("Number of teachers: ");
        int numTeachers = scanner.nextInt();
        scanner.nextLine();

        System.out.print("Number of students: ");
        int numStudents = scanner.nextInt();
        scanner.nextLine();

        System.out.print("Number of courses: ");
        int numCourses = scanner.nextInt();
        scanner.nextLine();

        // Insert employees
        List<Employee> teachers = new ArrayList<>();
        for (int i = 1; i <= numTeachers; i++) {
            System.out.print("Enter the name of Teacher" + " " + i + ": ");
            String teacherName = scanner.nextLine();
            teachers.add(new Employee(teacherName));
        }

        // Insert students
        List<Student> students = new ArrayList<>();
        for (int i = 1; i <= numStudents; i++) {
            System.out.print("Enter the name of Student" + " " + i + ": ");
            String studentName = scanner.nextLine();

            System.out.print("Enter the student ID of Student" + " " + i + ": ");
            int studentId = scanner.nextInt();
            scanner.nextLine();

            students.add(new Student(studentName, studentId));
        }

        // Insert courses
        List<Course> courses = new ArrayList<>();
        for (int i = 1; i <= numCourses; i++) {
            System.out.print("Enter the name of Course" +  " " + i + ": ");
            String courseName = scanner.nextLine();
            courses.add(new Course(courseName));
        }

        // Enroll students in courses
        for (Course course : courses) {
            for (Student student : students) {
                course.enrollStudent(student);
            }
        }

        // Insert marks
        for (Course course : courses) {
            for (Student student : students) {
                System.out.print("Enter the marks for " + student.getName() + " in " + course.getCourseName() + ": ");
                int marks = scanner.nextInt();
                course.putMarks(student, marks);
            }
        }
        // View enrolled students
        for (Course course1 : courses) {
            course1.viewStudents();
        }

        for (Employee teacher : teachers) {
            System.out.print("Enter a message from " + teacher.getName() + " to another teacher: ");
            String message = scanner.nextLine();
            for (Employee recipient : teachers) {
                if (!teacher.equals(recipient)) {
                    teacher.sendMessage(recipient, message);
                }
            }

            System.out.print("Enter a complaint from " + teacher.getName() + ": ");
            String complaint = scanner.nextLine();
            teacher.sendComplaint(complaint);
        }

        scanner.close();
    }
}

@Entity
public class Student {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    private String fatherName;
    private String motherName;
    private int age;
    private String address;
    private String className;
    private String school;
    private String area;
    private double marks;
    private String grade;
    private boolean passed;
    private boolean promoted;

    // Getters and Setters
}
--------------------------------
@Repository
public interface StudentRepository extends JpaRepository<Student, Long> {
    Optional<Student> findByName(String name);
}
----------------------------------
public interface StudentService {
    Student saveStudent(Student student);
    Optional<Student> getStudentByName(String name);
}
-------------------------------------
@Service
public class StudentServiceImpl implements StudentService {
    @Autowired
    private StudentRepository studentRepository;

    @Override
    public Student saveStudent(Student student) {
        return studentRepository.save(student);
    }

    @Override
    public Optional<Student> getStudentByName(String name) {
        return studentRepository.findByName(name);
    }
}
-----------------------------------------------
@Controller
public class StudentController {
    @Autowired
    private StudentService studentService;

    @GetMapping("/")
    public String showForm(Model model) {
        model.addAttribute("student", new Student());
        return "index";
    }

    @PostMapping("/submit")
    public String submitForm(@ModelAttribute Student student, Model model) {
        studentService.saveStudent(student);
        model.addAttribute("student", student);
        return "dashboard";
    }
}
---------------------------------------------
Index.html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Student Registration</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0-alpha1/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
    <div class="container">
        <h2>Enter Your Name</h2>
        <form action="/submit" method="post">
            <div class="mb-3">
                <label for="name" class="form-label">Name</label>
                <input type="text" class="form-control" id="name" name="name" required>
            </div>
            <button type="submit" class="btn btn-primary">Submit</button>
        </form>
    </div>
</body>
</html>
------------------------------------------------------
Dashboard.html
<!DOCTYPE html> 
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Student Dashboard</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0-alpha1/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
    <div class="container">
        <h2>Welcome, ${student.name}</h2>
        <h3>Personal Details</h3>
        <p><strong>Father's Name:</strong> ${student.fatherName}</p>
        <p><strong>Mother's Name:</strong> ${student.motherName}</p>
        <p><strong>Age:</strong> ${student.age}</p>
        <p><strong>Address:</strong> ${student.address}</p>

        <h3>Academic Information</h3>
        <p><strong>Class:</strong> ${student.className}</p>
        <p><strong>School:</strong> ${student.school}</p>
        <p><strong>Area:</strong> ${student.area}</p>

        <h3>Performance Metrics</h3>
        <p><strong>Marks:</strong> ${student.marks}</p>
        <p><strong>Grade:</strong> ${student.grade}</p>
        <p><strong>Passed:</strong> ${student.passed ? 'Yes' : 'No'}</p>
        <p><strong>Promoted:</strong> ${student.promoted ? 'Yes' : 'No'}</p>
    </div>
</body>
</html>
------------------------------------------------------------------
<div class="mb-3">
    <label for="fatherName" class="form-label">Father's Name</label>
    <input type="text" class="form-control" id="fatherName" name="fatherName">
</div>
<div class="mb-3">
    <label for="motherName" class="form-label">Mother's Name</label>
    <input type="text" class="form-control" id="motherName" name="motherName">
</div>
<div class="mb-3">
    <label for="age" class="form-label">Age</label>
    <input type="number" class="form-control" id="age" name="age">
</div>
--------------------------------------------------------------
<!DOCTYPE html>
<html>
<head>
    <title>All Students</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0-alpha1/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
<div class="container mt-4">
    <h2>All Registered Students</h2>
    <table class="table table-striped">
        <thead>
            <tr>
                <th>Name</th><th>Class</th><th>School</th><th>Marks</th><th>Grade</th><th>Promoted</th>
            </tr>
        </thead>
        <tbody>
            <tr th:each="student : ${students}">
                <td th:text="${student.name}"></td>
                <td th:text="${student.className}"></td>
                <td th:text="${student.school}"></td>
                <td th:text="${student.marks}"></td>
                <td th:text="${student.grade}"></td>
                <td th:text="${student.promoted ? 'Yes' : 'No'}"></td>
            </tr>
        </tbody>
    </table>
</div>
</body>
</html>
------------------------------------------------------------------------
@NotBlank
private String name;

@Min(5)
@Max(100)
private int age;
--------------------------------------
@PostMapping("/submit")
public String submitForm(@Valid @ModelAttribute Student student, BindingResult result, Model model) {
    if (result.hasErrors()) {
        return "index";
    }
    studentService.saveStudent(student);
    model.addAttribute("student", student);
    return "dashboard";
}



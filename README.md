import java.util.*;

public class OnlineExamSystem {

    static class User {
        String username;
        String password;
        String fullName;
        String email;

        public User(String username, String password, String fullName, String email) {
            this.username = username;
            this.password = password;
            this.fullName = fullName;
            this.email = email;
        }
    }

    static class Question {
        String questionText;
        String[] options;
        int correctOption; // 1-based index

        public Question(String questionText, String[] options, int correctOption) {
            this.questionText = questionText;
            this.options = options;
            this.correctOption = correctOption;
        }
    }

    private static Scanner scanner = new Scanner(System.in);
    private static User currentUser = null;
    private static List<Question> questions = new ArrayList<>();
    private static Map<Integer, Integer> userAnswers = new HashMap<>();
    private static final int EXAM_TIME_SECONDS = 30; // exam duration in seconds

    public static void main(String[] args) {
        // Demo user
        User demoUser = new User("student1", "pass123", "John Doe", "john@example.com");

        // Sample MCQs
        questions.add(new Question("What is the capital of France?",
                new String[]{"1) Berlin", "2) London", "3) Paris", "4) Madrid"}, 3));
        questions.add(new Question("2 + 2 = ?",
                new String[]{"1) 3", "2) 4", "3) 5", "4) 6"}, 2));
        questions.add(new Question("Which language is this program written in?",
                new String[]{"1) Python", "2) Java", "3) C++", "4) Ruby"}, 2));

        System.out.println("=== Welcome to Online Exam System ===\n");

        // Login loop
        while (currentUser == null) {
            System.out.print("Enter username: ");
            String username = scanner.nextLine().trim();
            System.out.print("Enter password: ");
            String password = scanner.nextLine().trim();

            if (username.equals(demoUser.username) && password.equals(demoUser.password)) {
                currentUser = demoUser;
                System.out.println("Login successful! Welcome, " + currentUser.fullName + "\n");
            } else {
                System.out.println("Invalid username or password. Try again.\n");
            }
        }

        mainMenu();
    }

    private static void mainMenu() {
        while (true) {
            System.out.println("\nMain Menu:");
            System.out.println("1. Update Profile");
            System.out.println("2. Update Password");
            System.out.println("3. Start Exam");
            System.out.println("4. Logout");
            System.out.print("Choose an option: ");

            String choice = scanner.nextLine().trim();

            switch (choice) {
                case "1":
                    updateProfile();
                    break;
                case "2":
                    updatePassword();
                    break;
                case "3":
                    startExam();
                    break;
                case "4":
                    logout();
                    return;
                default:
                    System.out.println("Invalid option, please try again.");
            }
        }
    }

    private static void updateProfile() {
        System.out.println("\n--- Update Profile ---");
        System.out.print("Enter full name (current: " + currentUser.fullName + "): ");
        String newName = scanner.nextLine().trim();
        if (!newName.isEmpty()) {
            currentUser.fullName = newName;
        }

        System.out.print("Enter email (current: " + currentUser.email + "): ");
        String newEmail = scanner.nextLine().trim();
        if (!newEmail.isEmpty()) {
            currentUser.email = newEmail;
        }

        System.out.println("Profile updated successfully!");
    }

    private static void updatePassword() {
        System.out.println("\n--- Update Password ---");
        System.out.print("Enter current password: ");
        String oldPass = scanner.nextLine().trim();

        if (!oldPass.equals(currentUser.password)) {
            System.out.println("Incorrect current password!");
            return;
        }

        System.out.print("Enter new password: ");
        String newPass = scanner.nextLine().trim();

        System.out.print("Confirm new password: ");
        String confirmPass = scanner.nextLine().trim();

        if (!newPass.equals(confirmPass)) {
            System.out.println("Passwords do not match!");
            return;
        }

        currentUser.password = newPass;
        System.out.println("Password updated successfully!");
    }

    private static void startExam() {
        System.out.println("\nStarting exam! You have " + EXAM_TIME_SECONDS + " seconds.");

        userAnswers.clear();

        Thread timer = new Thread(() -> {
            try {
                Thread.sleep(EXAM_TIME_SECONDS * 1000L);
                System.out.println("\nTime is up! Auto-submitting your exam...");
                submitExam();
                System.exit(0);
            } catch (InterruptedException ignored) {}
        });

        timer.start();

        // Loop through questions
        for (int i = 0; i < questions.size(); i++) {
            Question q = questions.get(i);
            System.out.println("\nQ" + (i + 1) + ": " + q.questionText);
            for (String option : q.options) {
                System.out.println(option);
            }

            int answer = -1;
            while (answer < 1 || answer > q.options.length) {
                System.out.print("Your answer (1-" + q.options.length + "): ");
                String input = scanner.nextLine().trim();
                try {
                    answer = Integer.parseInt(input);
                    if (answer < 1 || answer > q.options.length) {
                        System.out.println("Invalid choice. Try again.");
                    }
                } catch (NumberFormatException e) {
                    System.out.println("Please enter a number.");
                }
            }
            userAnswers.put(i, answer);
        }

        // Stop timer and submit
        timer.interrupt();
        submitExam();
    }

    private static void submitExam() {
        int score = 0;
        for (int i = 0; i < questions.size(); i++) {
            Question q = questions.get(i);
            int userAns = userAnswers.getOrDefault(i, -1);
            if (userAns == q.correctOption) {
                score++;
            }
        }

        System.out.println("\n--- Exam Result ---");
        System.out.println("Score: " + score + "/" + questions.size());
    }

    private static void logout() {
        System.out.println("\nLogging out... Goodbye, " + currentUser.fullName + "!");
        currentUser = null;
    }
}# OnlineExamSystem

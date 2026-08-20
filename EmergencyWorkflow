import java.util.ArrayDeque;
import java.util.Deque;
import java.util.Scanner;

public class EmergencyWorkflow {

    // Stack สำหรับเก็บ Action ที่ทำไปแล้ว
    private Deque<Action> eventStack = new ArrayDeque<>();

    // Stack สำหรับเก็บ Action ที่ Undo ไปแล้ว
    private Deque<Action> redoStack = new ArrayDeque<>();

    // State ปัจจุบัน
    private State currentState = State.NEW;

    private Scanner scanner = new Scanner(System.in);

    public static void main(String[] args) {

        EmergencyWorkflow system = new EmergencyWorkflow();

        system.run();
    }

    // =========================
    // Main Menu
    // =========================

    public void run() {

        int choice;

        do {

            System.out.println();
            System.out.println("========================================");
            System.out.println("       EMERGENCY WORKFLOW SYSTEM");
            System.out.println("========================================");

            System.out.println("Current State: " + currentState);

            System.out.println();
            System.out.println("1. Add Action");
            System.out.println("2. Undo");
            System.out.println("3. Redo");
            System.out.println("4. Show Status");
            System.out.println("5. Exit");

            System.out.println("----------------------------------------");
            System.out.print("Enter choice: ");

            choice = scanner.nextInt();

            switch (choice) {

                case 1:
                    showActions();
                    break;

                case 2:
                    undo();
                    break;

                case 3:
                    redo();
                    break;

                case 4:
                    showStatus();
                    break;

                case 5:
                    System.out.println();
                    System.out.println("Program ended.");
                    break;

                default:
                    System.out.println();
                    System.out.println("ERROR: Invalid menu choice.");

            }

        } while (choice != 5);

        scanner.close();
    }

    // =========================
    // Add Action
    // =========================

    private void showActions() {

        if (currentState == State.CLOSED) {

            System.out.println();
            System.out.println("ERROR: Case is already CLOSED.");
            System.out.println("Cannot add new Action.");

            return;
        }

        System.out.println();
        System.out.println("========== Available Actions ==========");

        System.out.println("1. CALL_RECEIVED");
        System.out.println("2. TEAM_ASSIGNED");
        System.out.println("3. VEHICLE_DISPATCHED");
        System.out.println("4. ARRIVED_AT_SCENE");
        System.out.println("5. CASE_CLOSED");

        System.out.print("Enter Action: ");

        int choice = scanner.nextInt();

        Action action;

        switch (choice) {

            case 1:
                action = Action.CALL_RECEIVED;
                break;

            case 2:
                action = Action.TEAM_ASSIGNED;
                break;

            case 3:
                action = Action.VEHICLE_DISPATCHED;
                break;

            case 4:
                action = Action.ARRIVED_AT_SCENE;
                break;

            case 5:
                action = Action.CASE_CLOSED;
                break;

            default:
                System.out.println("ERROR: Invalid Action.");
                return;
        }

        addAction(action);
    }

    // =========================
    // Algorithm B
    // Event Stack + State Machine
    // =========================

    private void addAction(Action action) {

        State nextState = getNextState(currentState, action);

        // ตรวจสอบว่า Action ถูกต้องตาม State หรือไม่
        if (nextState == null) {

            System.out.println();
            System.out.println("ERROR: Invalid Action!");
            System.out.println("Current State: " + currentState);
            System.out.println("Action: " + action);

            return;
        }

        // เพิ่ม Action เข้า Event Stack
        eventStack.push(action);

        // เปลี่ยน State
        currentState = nextState;

        // เพิ่ม Action ใหม่หลัง Undo
        // ต้องล้าง Redo Stack
        redoStack.clear();

        System.out.println();
        System.out.println("SUCCESS!");

        System.out.println("Action: " + action);
        System.out.println("Current State: " + currentState);

        System.out.println("Redo Stack: CLEARED");
    }

    // =========================
    // Transition Table
    // =========================

    private State getNextState(State state, Action action) {

        switch (state) {

            case NEW:

                if (action == Action.CALL_RECEIVED) {
                    return State.RECEIVED;
                }

                break;

            case RECEIVED:

                if (action == Action.TEAM_ASSIGNED) {
                    return State.ASSIGNED;
                }

                break;

            case ASSIGNED:

                if (action == Action.VEHICLE_DISPATCHED) {
                    return State.DISPATCHED;
                }

                break;

            case DISPATCHED:

                if (action == Action.ARRIVED_AT_SCENE) {
                    return State.ON_SCENE;
                }

                break;

            case ON_SCENE:

                if (action == Action.CASE_CLOSED) {
                    return State.CLOSED;
                }

                break;

            case CLOSED:

                return null;
        }

        return null;
    }

    // =========================
    // Undo
    // =========================

    private void undo() {

        if (eventStack.isEmpty()) {

            System.out.println();
            System.out.println("ERROR: Event Stack is empty.");
            System.out.println("Cannot Undo.");

            return;
        }

        Action action = eventStack.pop();

        // เก็บ Action ที่ Undo ไว้ใน Redo Stack
        redoStack.push(action);

        // คำนวณ State ใหม่จาก Event Stack
        rebuildState();

        System.out.println();
        System.out.println("========== UNDO ==========");

        System.out.println("Undo Action: " + action);

        System.out.println("Current State: " + currentState);
    }

    // =========================
    // Redo
    // =========================

    private void redo() {

        if (redoStack.isEmpty()) {

            System.out.println();
            System.out.println("ERROR: Redo Stack is empty.");
            System.out.println("Cannot Redo.");

            return;
        }

        Action action = redoStack.pop();

        State nextState = getNextState(currentState, action);

        if (nextState == null) {

            System.out.println();
            System.out.println("ERROR: Cannot Redo this Action.");

            redoStack.push(action);

            return;
        }

        eventStack.push(action);

        currentState = nextState;

        System.out.println();
        System.out.println("========== REDO ==========");

        System.out.println("Redo Action: " + action);

        System.out.println("Current State: " + currentState);
    }

    // =========================
    // Rebuild State
    // =========================

    private void rebuildState() {

        currentState = State.NEW;

        // Event Stack ต้องเรียงจากเก่าสุด -> ใหม่สุด
        Action[] actions = eventStack.toArray(new Action[0]);

        for (int i = actions.length - 1; i >= 0; i--) {

            State nextState = getNextState(currentState, actions[i]);

            if (nextState != null) {
                currentState = nextState;
            }
        }
    }

    // =========================
    // Show Status
    // =========================

    private void showStatus() {

        System.out.println();
        System.out.println("========================================");
        System.out.println("                 STATUS");
        System.out.println("========================================");

        System.out.println("Current State: " + currentState);

        System.out.println();

        System.out.println("Event Stack:");

        if (eventStack.isEmpty()) {

            System.out.println("EMPTY");

        } else {

            for (Action action : eventStack) {
                System.out.println("- " + action);
            }
        }

        System.out.println();

        System.out.println("Redo Stack:");

        if (redoStack.isEmpty()) {

            System.out.println("EMPTY");

        } else {

            for (Action action : redoStack) {
                System.out.println("- " + action);
            }
        }

        System.out.println("========================================");
    }
}

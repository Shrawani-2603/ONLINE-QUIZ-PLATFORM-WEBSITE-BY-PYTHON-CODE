# ONLINE-QUIZ-PLATFORM-WEBSITE-BY-PYTHON-CODE

import pandas as pd
import time

# ================= USER DETAILS =================
Name = input("Enter your name: ").strip()
Email = input("Enter your email: ").strip()

# ================= LOAD QUESTIONS =================
def load_questions():
    file_path = "PYTHON LAB EXCEL.xlsx"

    try:
        df = pd.read_excel(file_path)
    except FileNotFoundError:
        print("❌ File not found.")
        return {}
    except Exception as e:
        print("❌ Error loading Excel file:", e)
        return {}

    # Normalize column names
    df.columns = df.columns.str.strip().str.lower().str.replace(" ", "")

    required_cols = [
        'subject', 'question',
        'optiona', 'optionb', 'optionc', 'optiond',
        'correctanswer'
    ]

    for col in required_cols:
        if col not in df.columns:
            print(f"❌ Missing column: '{col}'")
            return {}

    quiz_data = {}

    for _, row in df.iterrows():
        try:
            subject = str(row.get('subject', '')).strip().lower()
            question = str(row.get('question', '')).strip()

            if not subject or not question:
                continue

            options = (
                str(row.get('optiona', '')),
                str(row.get('optionb', '')),
                str(row.get('optionc', '')),
                str(row.get('optiond', ''))
            )

            correct = str(row.get('correctanswer', '')).strip().upper()

            if correct not in ['A', 'B', 'C', 'D']:
                continue

            level = str(row.get('level', 'easy')).lower()

            marks_value = row.get('marks', 1)
            try:
                marks = int(marks_value) if not pd.isna(marks_value) else 1
            except:
                marks = 1

            if subject not in quiz_data:
                quiz_data[subject] = []

            quiz_data[subject].append((question, options, correct, level, marks))

        except:
            continue

    return quiz_data


# ================= QUIZ FUNCTION =================
def run_quiz(questions):
    score = 0
    total = 0

    for q, options, correct, level, marks in questions:
        print("\n" + q)
        print("A.", options[0])
        print("B.", options[1])
        print("C.", options[2])
        print("D.", options[3])

        print("⏳ You have 10 seconds!")

        # ⏱ TIMER START
        start = time.time()
        ans = input("Enter answer (A/B/C/D): ")
        end = time.time()
        # ⏱ TIMER END

        # Check time
        if end - start > 10:
            print("⏰ Time exceeded! Answer ignored.")
            continue

        ans = ans.strip().upper()

        if ans not in ['A', 'B', 'C', 'D']:
            print("⚠ Invalid input! Question skipped.")
            continue

        total += marks

        if ans == correct:
            print("✅ Correct!")
            score += marks
        else:
            print(f"❌ Wrong! Correct answer: {correct}")

    return score, total


# ================= MAIN =================
def main():
    quiz_data = load_questions()

    if not quiz_data:
        print("❌ No quiz data found.")
        return

    subjects = list(quiz_data.keys())

    print("\n===== QUIZ =====")
    for i, sub in enumerate(subjects, 1):
        print(f"{i}. {sub.capitalize()}")
    print(f"{len(subjects)+1}. All")

    try:
        choice = int(input("Enter choice: ").strip())
    except:
        print("❌ Invalid input")
        return

    if choice == len(subjects) + 1:
        selected = subjects
    elif 1 <= choice <= len(subjects):
        selected = [subjects[choice - 1]]
    else:
        print("❌ Invalid choice")
        return

    total_score = 0
    total_marks = 0

    for sub in selected:
        print(f"\n--- {sub.upper()} QUIZ ---")
        score, marks = run_quiz(quiz_data[sub])
        total_score += score
        total_marks += marks

    print("\n===== RESULT =====")
    print("Name:", Name)
    print("Email:", Email)
    print("Score:", total_score, "/", total_marks)

    percent = (total_score / total_marks) * 100 if total_marks > 0 else 0
    print("Percentage:", round(percent, 2), "%")


# ================= RUN =================
if __name__ == "__main__":
    main()

class Student:
    def __init__(self, name, surname, courses=None):
        self.name = name
        self.surname = surname
        self.courses_in_progress = courses or []
        self.finished_courses = []
        self.grades = {}

    def rate_lecturer(self, lecturer, course, grade):
        if (isinstance(lecturer, Lecturer) and
                course in self.courses_in_progress and
                course in lecturer.courses_attached):

            if course not in lecturer.lecture_grades:
                lecturer.lecture_grades[course] = []
            lecturer.lecture_grades[course].append(grade)
        else:
            raise ValueError("Невозможно поставить оценку")

    def average_grade(self):
        if not self.grades:
            return 0
        all_grades = [grade for grades in self.grades.values() for grade in grades]
        return round(sum(all_grades) / len(all_grades), 1)

    def __str__(self):
        courses_in_progress = ', '.join(self.courses_in_progress) or "Нет"
        finished_courses = ', '.join(self.finished_courses) or "Нет"
        return (f"Имя: {self.name}\n"
                f"Фамилия: {self.surname}\n"
                f"Средняя оценка за ДЗ: {self.average_grade()}\n"
                f"Курсы в процессе: {courses_in_progress}\n"
                f"Завершенные курсы: {finished_courses}")

    def __lt__(self, other):
        if not isinstance(other, Student):
            raise TypeError("Можно сравнивать только студентов")
        return self.average_grade() < other.average_grade()

    def __eq__(self, other):
        if not isinstance(other, Student):
            raise TypeError("Можно сравнивать только студентов")
        return self.average_grade() == other.average_grade()

    def __le__(self, other):
        if not isinstance(other, Student):
            raise TypeError("Можно сравнивать только студентов")
        return self.average_grade() <= other.average_grade()


class Mentor:
    def __init__(self, name, surname, courses_attached=None):
        self.name = name
        self.surname = surname
        self.courses_attached = courses_attached or []


class Reviewer(Mentor):
    def rate_hw(self, student, course, grade):
        if (isinstance(student, Student) and
                course in self.courses_attached and
                course in student.courses_in_progress):

            if course not in student.grades:
                student.grades[course] = []
            student.grades[course].append(grade)
        else:
            raise ValueError("Невозможно поставить оценку")

    def __str__(self):
        return (f"Имя: {self.name}\n"
                f"Фамилия: {self.surname}")


class Lecturer(Mentor):
    def __init__(self, name, surname, courses_attached=None):
        super().__init__(name, surname, courses_attached)
        self.lecture_grades = {}

    def average_lecture_grade(self):
        if not self.lecture_grades:
            return 0
        all_grades = [grade for grades in self.lecture_grades.values() for grade in grades]
        return round(sum(all_grades) / len(all_grades), 1)

    def __str__(self):
        return (f"Имя: {self.name}\n"
                f"Фамилия: {self.surname}\n"
                f"Средняя оценка за лекции: {self.average_lecture_grade()}")

    def __lt__(self, other):
        if not isinstance(other, Lecturer):
            raise TypeError("Можно сравнивать только лекторов")
        return self.average_lecture_grade() < other.average_lecture_grade()

    def __eq__(self, other):
        if not isinstance(other, Lecturer):
            raise TypeError("Можно сравнивать только лекторов")
        return self.average_lecture_grade() == other.average_lecture_grade
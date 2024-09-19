#include <iostream>
#include <fstream>
#include <vector>
#include <string>
#include <sstream>
#include <iomanip>

class Question {
public:
    std::string questionText;
    std::string correctAnswer;

    Question(const std::string& text, const std::string& answer)
        : questionText(text), correctAnswer(answer) {}
};

class Quiz {
private:
    std::vector<Question> questions;
    std::string quizFile;

public:
    Quiz(const std::string& fileName) : quizFile(fileName) {
        loadQuestions();
    }

    void loadQuestions() {
        std::ifstream file(quizFile);
        if (!file.is_open()) {
            std::cerr << "Error opening file: " << quizFile << std::endl;
            return;
        }

        std::string line;
        while (std::getline(file, line)) {
            std::string questionText;
            std::string answerText;
            std::istringstream iss(line);
            std::getline(iss, questionText, '|');
            std::getline(iss, answerText);
            questions.emplace_back(questionText, answerText);
        }

        file.close();
    }

    void takeQuiz() {
        int score = 0;
        std::string userAnswer;

        for (const auto& question : questions) {
            std::cout << question.questionText << std::endl;
            std::cout << "Your answer: ";
            std::getline(std::cin, userAnswer);
            
            if (userAnswer == question.correctAnswer) {
                score++;
            }
        }

        std::cout << "Your score: " << score << "/" << questions.size() << std::endl;
        saveScore(score);
    }

    void saveScore(int score) {
        std::ofstream leaderboard("leaderboard.txt", std::ios::app);
        if (leaderboard.is_open()) {
            std::string name;
            std::cout << "Enter your name: ";
            std::getline(std::cin, name);
            leaderboard << name << ": " << score << std::endl;
            leaderboard.close();
        } else {
            std::cerr << "Error saving score." << std::endl;
        }
    }

    void showLeaderboard() {
        std::ifstream leaderboard("leaderboard.txt");
        if (!leaderboard.is_open()) {
            std::cerr << "Error opening leaderboard." << std::endl;
            return;
        }

        std::string line;
        std::cout << "Leaderboard:" << std::endl;
        while (std::getline(leaderboard, line)) {
            std::cout << line << std::endl;
        }

        leaderboard.close();
    }
};

int main() {
    std::cout << "Welcome to the Quiz Application!" << std::endl;
    std::string fileName;

    std::cout << "Enter the quiz file name: ";
    std::getline(std::cin, fileName);

    Quiz quiz(fileName);
    
    while (true) {
        int choice;
        std::cout << "1. Take Quiz\n2. Show Leaderboard\n3. Exit\n";
        std::cout << "Choose an option: ";
        std::cin >> choice;
        std::cin.ignore(); // To ignore the newline after the integer input

        switch (choice) {
            case 1:
                quiz.takeQuiz();
                break;
            case 2:
                quiz.showLeaderboard();
                break;
            case 3:
                std::cout << "Goodbye!" << std::endl;
                return 0;
            default:
                std::cout << "Invalid choice. Please try again." << std::endl;
        }
    }
}

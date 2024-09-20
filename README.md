#include <iostream>
#include <vector>
#include <string>
#include <fstream>

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

public:
    Quiz() {
        // Predefined questions
        questions.emplace_back("What is the capital of France?", "Paris");
        questions.emplace_back("What is 2 + 2?", "4");
        questions.emplace_back("What is the largest ocean on Earth?", "Pacific Ocean");
        questions.emplace_back("Who wrote 'Hamlet'?", "William Shakespeare");
        questions.emplace_back("What is the chemical symbol for water?", "H2O");
    }

    void takeQuiz() {
        int score = 0;
        std::string userAnswer;
        std::string name;

        std::cout << "Enter your name: ";
        std::getline(std::cin, name);

        for (const auto& question : questions) {
            std::cout << question.questionText << std::endl;
            std::cout << "Your answer: ";
            std::getline(std::cin, userAnswer);
            
            if (userAnswer == question.correctAnswer) {
                score++;
            }
        }

        std::cout << "Your score: " << score << "/" << questions.size() << std::endl;
        saveScore(name, score);
    }

    void saveScore(const std::string& name, int score) {
        std::ofstream leaderboard("leaderboard.txt", std::ios::app);
        if (leaderboard.is_open()) {
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

    void showQuestions() {
        std::cout << "Questions in the quiz:\n";
        for (const auto& question : questions) {
            std::cout << "- " << question.questionText << std::endl;
        }
    }
};

int main() {
    std::cout << "Welcome to the Quiz Application!" << std::endl;

    Quiz quiz;

    while (true) {
        int choice;
        std::cout << "\n1. Take Quiz\n2. Show Questions\n3. Show Leaderboard\n4. Exit\n";
        std::cout << "Choose an option: ";
        std::cin >> choice;
        std::cin.ignore(); 

        switch (choice) {
            case 1:
                quiz.takeQuiz();
                break;
            case 2:
                quiz.showQuestions();
                break;
            case 3:
                quiz.showLeaderboard();
                break;
            case 4:
                std::cout << "Goodbye!" << std::endl;
                return 0;
            default:
                std::cout << "Invalid choice. Please try again." << std::endl;
        }
    }
}

#include <iostream>
#include <fstream>
#include <map>
#include <vector>
#include <algorithm>

using namespace std;

int main() {
    // Выбор способа ввода текста
    cout << "1 - Vvedite text\n2 - Zagruzit iz file\nVyberite: ";
    int choice;
    cin >> choice;
    cin.ignore(); // Очистка буфера

    string text;
    if (choice == 1) {
        cout << "Vvedite text:\n";
        getline(cin, text);
    }
    else if (choice == 2) {
        cout << "Vvedite name file: ";
        string filename;
        getline(cin, filename);

        ifstream file(filename);
        if (!file) {
            cout << "Error open file!\n";
            return 1;
        }
        getline(file, text, '\0'); // Читаем весь файл
    }
    else {
        cout << "Wrong choice!\n";
        return 1;
    }

    if (text.empty()) {
        cout << "The text is empty!\n";
        return 0;
    }

    // Подсчет статистики
    map<string, int> words;
    map<char, int> letters;
    int word_count = 0;
    int char_count = 0;

    string current_word;
    for (char c : text) {
        if (isalpha(c)) {
            char lower_c = tolower(c);
            letters[lower_c]++;
            char_count++;
            current_word += lower_c;
        }
        else if (!current_word.empty()) {
            words[current_word]++;
            word_count++;
            current_word.clear();
        }
    }
    if (!current_word.empty()) {
        words[current_word]++;
        word_count++;
    }

    // Вывод результатов
    cout << "\nResult analis:\n";
    cout << "Total words: " << word_count << endl;
    cout << "Total letters: " << char_count << endl;

    // Топ-5 букв
    vector<pair<char, int>> top_letters(letters.begin(), letters.end());
    sort(top_letters.begin(), top_letters.end(), [](auto &a, auto &b) {
        return a.second > b.second;
    });

    cout << "\nTop 5 letters:\n";
    for (int i = 0; i < min(5, (int)top_letters.size()); i++) {
        cout << top_letters[i].first << ": " << top_letters[i].second << endl;
    }

    // Повторяющиеся слова
    cout << "\nRepeated words:\n";
    bool has_repeats = false;
    for (auto &[word, count] : words) {
        if (count > 1) {
            cout << word << ": " << count << " raz\n";
            has_repeats = true;
        }
    }
    if (!has_repeats) cout << "Dont have repeated words:\n";

    // Сохранение в файл
    cout << "\nSave result? (y/n): ";
    char save;
    cin >> save;
    if (tolower(save) == 'y') {
        cout << "Vvedite name file: ";
        string out_file;
        cin >> out_file;

        ofstream file(out_file);
        file << "Total words: " << word_count << endl;
        file << "Total letters: " << char_count << endl;

        file << "\nTop 5 letters:\n";
        for (int i = 0; i < min(5, (int)top_letters.size()); i++) {
            file << top_letters[i].first << ": " << top_letters[i].second << endl;
        }

        file << "\nRepeated words:\n";
        for (auto &[word, count] : words) {
            if (count > 1) file << word << ": " << count << " raz\n";
        }

        cout << "Result save in " << out_file << endl;
    }

    return 0;
}

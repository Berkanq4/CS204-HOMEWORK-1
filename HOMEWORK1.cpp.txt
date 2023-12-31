#include <iostream>
#include <vector>
#include <fstream>
#include <sstream>
using namespace std;



bool checkValid(ifstream& file) {
    string line;

    int columnCounterPrevious = -1;
    while (getline(file, line)) {


        int columnCounter = 0;
        istringstream ss(line);
        char ch;

        while (ss >> ch) {
            if (ch != '#' && ch != '-') {
                return false;
            }
            else {
                columnCounter++;

            }
        }
        //empty matrix check
        if (columnCounter == 0) {
            return false;
        }

        if (columnCounterPrevious != -1) {
            if (columnCounterPrevious != columnCounter) {
                return false;
            }
        }
        columnCounterPrevious = columnCounter;

    }
    //setting cursor to the beginning
    file.clear();
    file.seekg(0);
    return true;
}



void incrementIndexesByInput(vector<vector<int>>& vec, int input) {
    for (int i = 0;i < vec.size();i++) {
        if (vec[i].size() > 0) {
            for (int j = 0;j < vec[i].size();j++) {
                vec[i][j] += input;
            }
        }
    }
}


bool checkRowMatch(vector<char>& charRow, vector<int>& indexRow) {
    for (int index : indexRow) {
        if (index < charRow.size()) {
            if (charRow[index] == '#') {
                return false;
            }


        }
        else {
            return false;
        }

    }

    return true;

}


bool checkFormationMatch(vector<vector<char>>& source, vector<vector<int>>& indexes, int startingRowNum) {


    for (int j = 0;j < indexes.size();j++) {
        if (indexes[j].size() > 0) {

            if (!checkRowMatch(source[startingRowNum], indexes[j])) {
                return false;
            }

        }
        startingRowNum++;


    }



    return true;
}



void printFormation(vector<vector<int>>& indexVec, int startingRowNum, int placementNum) {
    cout << "Placement number " << placementNum << ":" << endl;
    for (int row = 0;row < indexVec.size();row++) {
        if (indexVec[row].size() > 0) {

            for (int i = 0;i < indexVec[row].size();i++) {

                cout << "(" << row + startingRowNum << "," << indexVec[row][i] << ") ";
            }

        }


    }
    cout << endl;
}



int main() {


    cout << "Please enter the file name:" << endl;
    string answer;
    cin >> answer;
    ifstream input;

    input.open(answer);
    while (input.fail()) {
        cout << "Could not open the file. Please enter a valid file name:" << endl;
        cin >> answer;
        input.open(answer);
    }



    if (!checkValid(input)) {
        cout << "Erroneous file content. Program terminates." << endl;
    }
    else {
        vector<vector<char>> myMatrix;

        string line;
        int row_counter = 0;
        int column_counter = 0;

        int NUM_COLS = 0;

        char ch;
        while (getline(input, line)) {
            row_counter++;
            istringstream ss(line);
            vector<char> rowVector;
            while (ss >> ch) {
                column_counter++;
                NUM_COLS = column_counter;
                rowVector.push_back(ch);


            }
            myMatrix.push_back((rowVector));
            column_counter = 0;

        }
        cout << "The matrix file contains:" << endl;
        for (int i = 0;i < row_counter;i++) {
            for (int j = 0;j < NUM_COLS;j++) {
                cout << myMatrix[i][j];
            }
            cout << endl;

        }

        string strQuery;
        cout << "Enter the string query for a shape, or \"Quit\" to terminate the program:" << endl;
        cin >> strQuery;





        while (strQuery != "Quit") {

            vector<vector<int>> queryIndexes;

            int amount = 0;
            int index = 0;



            vector<int> indexVector;
            for (int i = 0;i < strQuery.length();i++) {
                char ch = strQuery[i];

                if (ch == '/') {

                    queryIndexes.push_back(indexVector);
                    index = 0;
                    indexVector = {};

                }
                else if (isdigit(ch)) {

                    amount = ch - '0';
                }
                else if (ch == 'b') {
                    index += amount;
                }
                else if (ch == 's') {
                    for (int j = 0;j < amount;j++) {
                        indexVector.push_back(index);
                        index++;
                    }

                }


            }
            queryIndexes.push_back(indexVector);

            int rowNumToBeChecked = myMatrix.size() - queryIndexes.size() + 1;

            int possiblePushAmount = myMatrix[0].size() - queryIndexes[0][queryIndexes[0].size() - 1] - 1;

            //Check Normal and Pushed Version


            //Go 1 row Down

            //Check Normal and Pushed Version
            bool isFound = false;
            int placementNum = 1;


            for (int row = 0;row < rowNumToBeChecked;row++) {

                vector<vector<int>> pushedQueryIndexes = queryIndexes;
                for (int push = 0;push <= possiblePushAmount;push++) {

                    if (checkFormationMatch(myMatrix, pushedQueryIndexes, row)) {
                        printFormation(pushedQueryIndexes, row, placementNum);
                        placementNum++;
                        isFound = true;
                    }
                    incrementIndexesByInput(pushedQueryIndexes, 1);

                }






            }
            if (!isFound) {
                cout << "No placements found." << endl;
            }






            cout << "Enter the string query for a shape, or \"Quit\" to terminate the program:" << endl;
            cin >> strQuery;
        }
    }






    return 0;
}

---
title: Tube Lab as Class Solution
tags: [CS223, data structures]

---

```cpp
#include <iostream>
#include <stdexcept>

constexpr int BALLS_PER_COLOR = 2;
constexpr int TUBE_SIZE = 4;
constexpr int NUM_TUBES = 3;
constexpr char BLANK = ' ';
constexpr char COLORS[8] { 'B', 'G', 'R', 'P', 'A', 'M', 'C', 'E' };

class Tube {
  private:
    char values[TUBE_SIZE];
  public:
    class TubeFullException {};
    class TubeEmptyException {};
    Tube();  // Initialize as empty (was in fill_empty)
    // Static array field, don't need special copy constructor
    bool isEmpty() const;
    bool isFull() const;
    char& operator[](int idx) { 
        throw_if_out_of_bounds(idx);
        return values[idx]; 
    }
    const char& operator[](int idx) const { 
        throw_if_out_of_bounds(idx);
        return values[idx]; 
        }
    void put_ball(char c);
    char remove_ball();
    // we don't actually need these as friends because they will use []
    friend bool operator==(const Tube & t1, const Tube & t2);
    friend bool operator!=(const Tube & t1, const Tube & t2) { 
        return !(t1 == t2); 
    }
    friend std::ostream& operator<<(std::ostream& out, const Tube& t);
  private:
    void throw_if_out_of_bounds(int idx) const {
        if (idx < 0 || idx >= TUBE_SIZE) 
            throw std::out_of_range("Invalid tube index");
    }
};
```

```cpp
class TubeSet {
  private: 
    Tube tubes[NUM_TUBES];
  public:
    TubeSet() {} // <-- acts as fill_empty, nothing to do
    static TubeSet goal(); // <-- initialize as goal
    static TubeSet random(); // <-- initialize as random
    // Static array field, don't need special copy constructor
    Tube& operator[](int idx) { 
        throw_if_out_of_bounds(idx);
        return tubes[idx]; 
    }
    const Tube& operator[](int idx) const { 
        throw_if_out_of_bounds(idx);
        return tubes[idx]; 
    }
    void move_ball(int i, int j);
    static void printComparison(
        std::ostream& out, 
        const TubeSet& t1, 
        const TubeSet& t2);
    // we don't actually need these as friends because they will use []
    friend bool operator==(const TubeSet & ts1, const TubeSet & ts2);
    friend bool operator!=(const TubeSet & ts1, const TubeSet & ts2) { 
        return !(ts1 == ts2); 
    }
    friend std::ostream& operator<<(std::ostream& out, const TubeSet& ts);
  private:
    void throw_if_out_of_bounds(int idx) const {
        if (idx < 0 || idx >= NUM_TUBES) 
            throw std::out_of_range("Invalid tubeset index");
    }
};
```

```cpp
Tube::Tube() {
    for (int i = 0; i < TUBE_SIZE; i++) { values[i] = BLANK; }
}

bool Tube::isEmpty() const {
    return values[0] == BLANK;
}

bool Tube::isFull() const {
    return values[TUBE_SIZE - 1] != BLANK;
}

std::ostream& operator<<(std::ostream& out, const Tube& t) {
    for (int i = 0; i < TUBE_SIZE; i++) out << t[i];
    return out;
}

bool operator==(const Tube & t1, const Tube & t2) {
    for (int i = 0; i < TUBE_SIZE; i++) {
        if (t1[i] != t2[i]) return false;
    }
    return true;
}

void Tube::put_ball(char c) {
    for (int i = 0; i < TUBE_SIZE; i++) {
        if (values[i] == BLANK) {
            values[i] = c; 
            return;
        }
    }
    throw Tube::TubeFullException();
}

char Tube::remove_ball() {
    for (int i = TUBE_SIZE-1; i >= 0; i--) {
        if (values[i] != BLANK) {
            const char toReturn = values[i];
            values[i] = BLANK;
            return toReturn;
        }
    }
    throw Tube::TubeEmptyException();
}


TubeSet TubeSet::goal() {
    TubeSet ts;
    for (int i = 0; i < NUM_TUBES; i++) {
        for (int j = 0; j < BALLS_PER_COLOR; j++) {
            ts[i][j] = COLORS[i];
        }
    }
    return ts;
}

TubeSet TubeSet::random() {
    TubeSet ts;
    const int TOTAL_BALLS = NUM_TUBES * BALLS_PER_COLOR;
    char allBalls[TOTAL_BALLS];
    for (int i = 0; i < NUM_TUBES; i++) {
        for (int j = 0; j < BALLS_PER_COLOR; j++) {
            allBalls[i * BALLS_PER_COLOR + j] = COLORS[i];
        }
    }
    for (int remaining = TOTAL_BALLS; remaining > 0; remaining--) {
        int randIndex = rand() % remaining;
        char nextBall = allBalls[randIndex];
        allBalls[randIndex] = allBalls[remaining - 1];
        while (true) {
            int tubeIndex = rand() % NUM_TUBES;
            Tube& t = ts[tubeIndex];
            if (!t.isFull()) {
                t.put_ball(nextBall);
                break;
            }
        }
    }
    return ts;
}


bool operator==(const TubeSet & ts1, const TubeSet & ts2) {
    for (int i = 0; i < NUM_TUBES; i++) {
        if (ts1[i] != ts2[i]) return false;
    }
    return true;
}

std::ostream& operator<<(std::ostream& out, const TubeSet& ts) {
    for (int i = 0; i < NUM_TUBES; i++) out << ts[i] << "\n";
    return out;
}

void TubeSet::move_ball(int i, int j) {
    Tube& from = (*this)[i];
    Tube& to = (*this)[j];
    if (to.isFull()) throw Tube::TubeFullException();
    to.put_ball(from.remove_ball());
}

void TubeSet::printComparison(std::ostream& out, const TubeSet& ts1, const TubeSet& ts2) {
    for (int i = 0; i < NUM_TUBES; i++) {
        out << i + 1 << ": " << ts1[i] << "   " << ts2[i] << "\n";
    }
}

int main() {
    srand(time(nullptr));
    TubeSet curr = TubeSet::random();
    TubeSet goal = TubeSet::goal();
    while (curr != goal) {
        TubeSet::printComparison(std::cout, curr, goal);
        std::cout << "Enter move: ";
        int from, to;
        std::cin >> from >> to;
        try {
            curr.move_ball(from - 1, to - 1);
        } catch (const std::out_of_range& e) {
            std::cout << "Error! " << e.what() << "\n";
        } catch (const Tube::TubeEmptyException& e) {
            std::cout << "Can't move from empty tube!\n";
        } catch (const Tube::TubeFullException& e) {
            std::cout << "Can't move to full tube!\n";
        }
    }
}
```
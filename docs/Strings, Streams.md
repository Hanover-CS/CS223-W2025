---
title: 'Strings, Streams'
tags: [CS223, data structures, presentation]

---

# Strings, Streams

---

## String methods

- `s.at(4)`
- `s.append(s2)`
- `s.append(s2, pos, n)`
- `s.insert(p, str)`, `s.replace(p, n, str)`
- `s.substr(p, n)`
- `s.clear()`, `s.empty()`
- `s.capacity()` vs `s.size()`
- `s.find(str/ch, p)` 
    - `string::npos`

---

## String and number conversions

- `to_string(45) = "45"`
- `stoi("45") = 45`
    - `size_t`: unsigned number type
- stringstreams for more control

---

## Streams

- `istream`: stream for input
    - `istringstream`, `ifstream`, `cin`
- `ostream`: stream for output
    - `ostringstream`, `ofstream`, `cout`
- `stream`: both input and output
    - Use the others if you will only do one

---

## String streams

```cpp
istringstream in("hi 4");
string s;
int n;
in >> s >> n;
cout << s << "\n" << n << "\n";
```

```cpp
ostringstream out;
out << "hi" << " there!";
cout << out.str();
```

---

- Read numbers from the user until negative, store in ostringstream. Then print the numbers and their sum, like so:

    ```
    45 + 23 + 12 + 124 = 204
    ```
- You can add the `" + "` parts when putting in the ostringstream.

---

## File streams

- `ifstream in(filename)`
    - `ifstream in` then `in.open(filename)`
- `ofstream out(filename)`
    - `ifstream out` then `out.open(filename)`
- Must close (unless program exits)

---

## Status checks

- `in.bad()` true if stream in bad state
- `in.fail()` true if bad or if operation failed
- `in.eof()` true if file reached end
- `in.good()` true if all above false
- `if (in)` same as `if (!in.fail())`

---

- Write in file the first 20 numbers and their squares:
    ```
    1 1
    2 4
    3 9
    4 16
    5 25
    ...
    ```

---

### file mode flags

```
cout << bitset<8>(ios::app) << endl;
cout << bitset<8>(ios::ate) << endl;
cout << bitset<8>(ios::binary) << endl;
cout << bitset<8>(ios::in) << endl;
cout << bitset<8>(ios::out) << endl;
cout << bitset<8>(ios::trunc) << endl;
cout << bitset<8>(ios::in | ios::trunc) << endl;
```

---

## I/O manipulators

- `dec`, `oct`, `hex`
- `fixed`, `scientific`
- `setw`, `setprecision`
- `showbase`, `noshowbase`
- `showpoint`

---

- Change previous program to vertically align better (and to read the number of results from the user):
    ```
     1   1
     2   4
     3   9
     4  16
     5  25
    ...
    11 121
    ```

---

- Write a program to read some numbers then print them and their sum as follows:
    ```
        4
      823
        6
      234
    +  23
    -----
     1090
    ```
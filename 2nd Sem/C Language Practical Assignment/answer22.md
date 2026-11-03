# 22. Create Matrix Class Using Templates — Menu-Driven Program (Product, Transpose, Sum, Difference)

### 1. Problem Understanding

Write a **template-based Matrix class** in C++ that works with **any numeric type** (int, float, double, …), then a **menu-driven** program performing:

| # | Operation | Rule |
|---|---|---|
| 1 | **Product** | A is m×n, B must be n×p → result m×p |
| 2 | **Transpose** | m×n → n×m (swap rows/columns) |
| 3 | **Sum** | A and B must have the **same** dimensions |
| 4 | **Difference** | Same dimension requirement |

**Why templates?** One `Matrix<T>` class can store `int`, `float`, or `double` matrices without rewriting the code. The compiler generates a concrete class per used type.

### 2. Key Concepts Used

| Concept | Explanation |
|---|---|
| Class template | `template <class T> class Matrix { ... };` |
| Dynamic allocation | `T **data` — 2D array of size rows×cols |
| Constructor/Destructor | Allocate in constructor, `delete[]` in destructor |
| Friend function | `operator>>`/`<<` need access to private members |
| Operator overloading | `+`, `-`, `*` for sum, difference, product |
| Menu + switch | Dispatch user choice |

### 3. The C++ Program (Source Code)

```cpp
#include <iostream>
using namespace std;

template <class T>
class Matrix
{
private:
    int rows, cols;
    T **data;

public:
    /* constructor */
    Matrix(int r, int c)
    {
        rows = r;
        cols = c;
        data = new T *[rows];
        for (int i = 0; i < rows; i++)
            data[i] = new T[cols];
    }

    /* destructor */
    ~Matrix()
    {
        for (int i = 0; i < rows; i++)
            delete[] data[i];
        delete[] data;
    }

    /* copy constructor: deep copy so returned objects own their own memory */
    Matrix(const Matrix &other)
    {
        rows = other.rows;
        cols = other.cols;
        data = new T *[rows];
        for (int i = 0; i < rows; i++)
        {
            data[i] = new T[cols];
            for (int j = 0; j < cols; j++)
                data[i][j] = other.data[i][j];
        }
    }

    /* copy assignment: deep copy with self-assignment check */
    Matrix &operator=(const Matrix &other)
    {
        if (this == &other)
            return *this;

        for (int i = 0; i < rows; i++)
            delete[] data[i];
        delete[] data;

        rows = other.rows;
        cols = other.cols;
        data = new T *[rows];
        for (int i = 0; i < rows; i++)
        {
            data[i] = new T[cols];
            for (int j = 0; j < cols; j++)
                data[i][j] = other.data[i][j];
        }
        return *this;
    }

    int getRows() const { return rows; }
    int getCols() const { return cols; }

    /* read the matrix */
    void read()
    {
        for (int i = 0; i < rows; i++)
            for (int j = 0; j < cols; j++)
                cin >> data[i][j];
    }

    /* display the matrix */
    void display() const
    {
        for (int i = 0; i < rows; i++)
        {
            for (int j = 0; j < cols; j++)
                cout << data[i][j] << " ";
            cout << endl;
        }
    }

    /* transpose: returns a new Matrix with rows and cols swapped */
    Matrix<T> transpose() const
    {
        Matrix<T> result(cols, rows);
        for (int i = 0; i < rows; i++)
            for (int j = 0; j < cols; j++)
                result.data[j][i] = data[i][j];
        return result;
    }

    /* sum: this + other (dimensions must match) */
    Matrix<T> operator+(const Matrix<T> &other) const
    {
        Matrix<T> res(rows, cols);
        for (int i = 0; i < rows; i++)
            for (int j = 0; j < cols; j++)
                res.data[i][j] = data[i][j] + other.data[i][j];
        return res;
    }

    /* difference: this - other (dimensions must match) */
    Matrix<T> operator-(const Matrix<T> &other) const
    {
        Matrix<T> res(rows, cols);
        for (int i = 0; i < rows; i++)
            for (int j = 0; j < cols; j++)
                res.data[i][j] = data[i][j] - other.data[i][j];
        return res;
    }

    /* product: this * other (this.cols must equal other.rows) */
    Matrix<T> operator*(const Matrix<T> &other) const
    {
        Matrix<T> res(rows, other.cols);
        for (int i = 0; i < rows; i++)
        {
            for (int j = 0; j < other.cols; j++)
            {
                res.data[i][j] = 0;
                for (int k = 0; k < cols; k++)
                    res.data[i][j] += data[i][k] * other.data[k][j];
            }
        }
        return res;
    }
};

int main()
{
    int r1, c1, r2, c2, choice;

    cout << "Enter dimensions of Matrix A (rows cols): ";
    cin >> r1 >> c1;
    cout << "Enter dimensions of Matrix B (rows cols): ";
    cin >> r2 >> c2;

    cout << "\nEnter elements of Matrix A (" << r1 << "x" << c1 << "):\n";
    Matrix<int> A(r1, c1);
    A.read();

    cout << "\nEnter elements of Matrix B (" << r2 << "x" << c2 << "):\n";
    Matrix<int> B(r2, c2);
    B.read();

    cout << "\nMatrix A:\n"; A.display();
    cout << "\nMatrix B:\n"; B.display();

    do {
        cout << "\n---------- MATRIX MENU ----------\n";
        cout << "1. Product\n";
        cout << "2. Transpose\n";
        cout << "3. Sum\n";
        cout << "4. Difference\n";
        cout << "5. Quit\n";
        cout << "Enter your choice: ";
        cin >> choice;

        switch (choice)
        {
            case 1:   /* product */
                if (c1 != r2)
                    cout << "Product not possible (A.cols != B.rows).\n";
                else
                {
                    Matrix<int> P = A * B;
                    cout << "\nProduct (A x B):\n";
                    P.display();
                }
                break;

            case 2:   /* transpose */
            {
                Matrix<int> T = A.transpose();
                cout << "\nTranspose of A:\n";
                T.display();
                break;
            }

            case 3:   /* sum */
                if (r1 != r2 || c1 != c2)
                    cout << "Sum not possible (dimensions differ).\n";
                else
                {
                    Matrix<int> S = A + B;
                    cout << "\nSum (A + B):\n";
                    S.display();
                }
                break;

            case 4:   /* difference */
                if (r1 != r2 || c1 != c2)
                    cout << "Difference not possible (dimensions differ).\n";
                else
                {
                    Matrix<int> D = A - B;
                    cout << "\nDifference (A - B):\n";
                    D.display();
                }
                break;

            case 5:
                cout << "Exiting...\n";
                break;

            default:
                cout << "Invalid choice! Try again.\n";
        }
    } while (choice != 5);

    return 0;
}
```

### 4. Line-by-Line Explanation of Key Parts

| Code | What it does |
|---|---|
| `template <class T> class Matrix` | Declares a **generic class** usable with any type `T` |
| `T **data;` | 2D dynamic array — pointer to rows, each row a pointer to columns |
| `data[i] = new T[cols];` | Allocates each row in the constructor |
| `~Matrix()` | **Destructor** — frees every row, then the row array (prevents memory leak) |
| `Matrix(const Matrix &other)` | **Deep-copy constructor** — allocates and copies every element, so returned matrices do not share memory |
| `Matrix &operator=(const Matrix &other)` | **Deep-copy assignment** — releases old memory, then copies the other matrix; handles self-assignment |
| `Matrix<T> transpose() const` | Returns a matrix with swapped dimensions; `result.data[j][i] = data[i][j]` |
| `Matrix<T> operator+(const Matrix<T> &other) const` | **Operator overload** — adds element-wise; must validate equal sizes before calling |
| `Matrix<T> operator-(const Matrix<T> &other) const` | Element-wise subtraction |
| `Matrix<T> operator*(const Matrix<T> &other) const` | **Triple loop** — standard matrix multiplication (rows × cols × inner) |
| `for k... res.data[i][j] += data[i][k] * other.data[k][j]` | The dot product of row `i` of A with column `j` of B |
| `Matrix<int> A(r1, c1);` | Instantiates the template with `T = int` |

### 5. Matrix Multiplication — the Algorithm

For `A[m×n] × B[n×p] = C[m×p]`:
```
C[i][j] = Σ(k=0 to n-1)  A[i][k] * B[k][j]
```
- Row `i` of A dotted with column `j` of B.
- Dimension check: **A's columns must equal B's rows** (`c1 == r2`).
- The result has **A's rows × B's columns**.

### 6. Dry Run

**A = [[1,2],[3,4]], B = [[5,6],[7,8]]**

**Product (A×B):**
```
C[0][0] = 1×5 + 2×7 = 19
C[0][1] = 1×6 + 2×8 = 22
C[1][0] = 3×5 + 4×7 = 43
C[1][1] = 3×6 + 4×8 = 50
```
```
A x B = | 19  22 |
        | 43  50 |
```

**Sum:**
```
| 1+5  2+6 |   =   | 6   8 |
| 3+7  4+8 |       | 10  12 |
```

**Difference (A−B):**
```
| 1-5  2-6 |   =   | -4  -4 |
| 3-7  4-8 |       | -4  -4 |
```

**Transpose of A:**
```
| 1  3 |
| 2  4 |
```

### 7. Sample Run

```
Enter dimensions of Matrix A (rows cols): 2 2
Enter dimensions of Matrix B (rows cols): 2 2

Enter elements of Matrix A (2x2):
1 2
3 4

Enter elements of Matrix B (2x2):
5 6
7 8

Matrix A:
1 2
3 4

Matrix B:
5 6
7 8

---------- MATRIX MENU ----------
1. Product
...
Enter your choice: 1
Product (A x B):
19 22
43 50

Enter your choice: 3
Sum (A + B):
6 8
10 12

Enter your choice: 2
Transpose of A:
1 3
2 4

Enter your choice: 5
Exiting...
```

### 8. Edge Cases Table

| Case | Behaviour |
|---|---|
| c1 != r2 (product) | Guarded — prints "Product not possible" |
| Different dims (sum/difference) | Guarded — prints message |
| Square matrices | All operations work |
| 1×1 matrices | Behaves like scalar arithmetic |
| Rectangular matrices | Transpose swaps dims correctly |
| Non-numeric type (e.g., `Matrix<char>`) | Works via templates, but sum/product of chars is odd — intended for numbers |

### 9. Common Mistakes to Avoid

1. **Missing dimension checks** — matrix product and sum crash or read out-of-bounds memory if dimensions don't match. Always validate first.
2. **Transpose in-place on a rectangular matrix** — the result has different dimensions, so it **must** go into a new matrix (as done here).
3. **Not freeing memory in the destructor** — memory leak. Also, without the deep-copy constructor/assignment included above, the compiler-generated copy would share the same `data` pointer (shallow copy → double free). The main version now implements deep copy; the `vector` version avoids the issue entirely.
4. **Initialising `res.data[i][j] = 0;` before the k-loop** — otherwise the product accumulates garbage.
5. **Template code split across .cpp files** — templates must be fully visible at instantiation (header or same file).
6. **Integer overflow** for large products — use `double` or `long long` as the type parameter.

### 10. Safer Version — using `std::vector` (avoids manual memory + copy problems)

```cpp
#include <iostream>
#include <vector>
using namespace std;

template <class T>
class Matrix
{
    int rows, cols;
    vector<vector<T>> data;

public:
    Matrix(int r, int c) : rows(r), cols(c)
    {
        data.resize(r, vector<T>(c, 0));
    }

    void read()
    {
        for (int i = 0; i < rows; i++)
            for (int j = 0; j < cols; j++)
                cin >> data[i][j];
    }

    void display() const
    {
        for (int i = 0; i < rows; i++)
        {
            for (int j = 0; j < cols; j++)
                cout << data[i][j] << " ";
            cout << endl;
        }
    }

    Matrix<T> transpose() const
    {
        Matrix<T> result(cols, rows);
        for (int i = 0; i < rows; i++)
            for (int j = 0; j < cols; j++)
                result.data[j][i] = data[i][j];
        return result;
    }

    Matrix<T> operator+(const Matrix<T> &o) const
    {
        Matrix<T> r(rows, cols);
        for (int i = 0; i < rows; i++)
            for (int j = 0; j < cols; j++)
                r.data[i][j] = data[i][j] + o.data[i][j];
        return r;
    }

    Matrix<T> operator-(const Matrix<T> &o) const
    {
        Matrix<T> r(rows, cols);
        for (int i = 0; i < rows; i++)
            for (int j = 0; j < cols; j++)
                r.data[i][j] = data[i][j] - o.data[i][j];
        return r;
    }

    Matrix<T> operator*(const Matrix<T> &o) const
    {
        Matrix<T> r(rows, o.cols);
        for (int i = 0; i < rows; i++)
            for (int j = 0; j < o.cols; j++)
                for (int k = 0; k < cols; k++)
                    r.data[i][j] += data[i][k] * o.data[k][j];
        return r;
    }
};
```
*(The `main()` stays the same — this version is memory-safe and copy-safe.)*

### 11. Brief Result Table (for the lab record)

| Operation | Input A = [[1,2],[3,4]], B = [[5,6],[7,8]] |
|---|---|
| Product (A×B) | [[19,22],[43,50]] |
| Sum (A+B) | [[6,8],[10,12]] |
| Difference (A−B) | [[−4,−4],[−4,−4]] |
| Transpose of A | [[1,3],[2,4]] |

---

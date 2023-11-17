---
{"dg-publish":true,"permalink":"/algorithms/sorting/bubble-sort/"}
---



- Following are different implementations of Bubble Sort in Cpp
```cpp


void bub_sort_v1(std::vector<int> &vec) {
  int N = vec.size();
  bool swapped = true;
  while (swapped) {
    swapped = false;
    for (int i = 0; i < N - 1; i++) {
      if (vec[i] > vec[i + 1]) {
        swap(vec[i], vec[i + 1]);
        swapped = true;
      }
      print_vec(vec);
    }
  }
}

void bub_sort_v2(std::vector<int> &vec) {
  int N = vec.size();
  for (; N > 0; N--) {
    for (int i = 0; i < N - 1; i++) {
      if (vec[i] > vec[i + 1]) {
        swap(vec[i], vec[i + 1]);
      }
    }
  }
}

void _bub_sort_v4(std::vector<int> &vec, const int n, int i) {
  std::cout << "Inner:";
  print_vec(vec);
  if (i == n - 1)
    return;
  if (vec[i] < vec[i + 1]) {
    swap(vec[i], vec[i + 1]);
    _bub_sort_v4(vec, n, i + 1);
  }
  _bub_sort_v4(vec, n, i + 1);
}

void bub_sort_v4(std::vector<int> &vec, int n) {
  std::cout << "Outer:";
  print_vec(vec);
  if (n == 1)
    return;
  _bub_sort_v4(vec, n, 0);
  bub_sort_v4(vec, n - 1);
}

int main() {

  std::vector<int> vec = {13, 4, 5, 2, 5, 2, 64, 0};
  //   bub_sort_v1(vec);
  bub_sort_v4(vec, vec.size());
  print_vec(vec);
  return 0;
}
```


---
{"dg-publish":true,"permalink":"/algorithms/sorting/selection-sort/"}
---


```cpp

int get_min(std::vector<int> &vec, int idx) {
  int min = vec[idx];
  int n = vec.size();
  for (int i = idx; i < vec.size(); i++) {
    if (min > vec[i]) {
      min = vec[i];
      idx = i;
    }
  }
  return idx;
}

void selection_sort(std::vector<int> &vec) {
  print_vec(vec);
  int n = vec.size();
  for (int i = 0; i < n; i++) {
    int idx = get_min(vec, i);
    swap(vec[i], vec[idx]);
  }
  print_vec(vec);
  return;
}

void _r_get_min(std::vector<int> &vec, int &min, int &idx, int i) {
  std::cout << min << "," << idx << std::endl;
  if (i > vec.size() - 2)
    return;
  if (min > vec[i + 1]) {
    min = vec[i + 1];
    i = i + 1;
    idx = i;
    _r_get_min(vec, min, idx, i);
  }
  _r_get_min(vec, min, idx, i + 1);
}

int r_get_min(std::vector<int> &vec, int idx) {
  int i = idx;
  int min = vec[idx];
  _r_get_min(vec, min, idx, i);
  return idx;
}

void _selection_sort_v2(std::vector<int> &vec, int iteration) {
  print_vec(vec);
  if (iteration == vec.size() - 1)
    return;
  int idx = r_get_min(vec, iteration);
  swap(vec[iteration], vec[idx]);
  _selection_sort_v2(vec, iteration + 1);
}

void selection_sort_v2(std::vector<int> &vec) {
  print_vec(vec);
  int n = vec.size();
  int iteration = 0;
  _selection_sort_v2(vec, iteration);
  return;
}

int main() {
  std::vector<int> vec = {2, 5, -643, 234, 21, 64, 24, 54, 6};
  selection_sort_v2(vec);

  return 0;
}
```
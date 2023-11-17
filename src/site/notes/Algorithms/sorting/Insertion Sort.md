---
{"dg-publish":true,"permalink":"/algorithms/sorting/insertion-sort/"}
---

```cpp
void insertionSort(std::vector<int>& vec) {
for(int i = 1; i<vec.size()-1; i++) {
	int X = a[i];
	j = i -1;
	for(;j>=0&&a[j]>X;j--) {
		a[j] = a[j+1];
		a[j+1] = X;
	}


}
int main() {

	std::vector<int> vect = rand_vec(10);
	print_vec(vect);
	insertionSort(vect);
	print_vec(vect);
	return 0;
}

```
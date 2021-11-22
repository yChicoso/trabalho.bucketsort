#include <stdio.h>
#include <stdlib.h>

#define NARRAY 7   // tamanho do array
#define NBUCKET 6  // numero de baldes
#define INTERVAL 10  // capacidade de cada balde

struct Node {
  int data;
  struct Node *next;
};

void BucketSort(int arr[]);
struct Node *InsertionSort(struct Node *list);
void print(int arr[]);
void printBuckets(struct Node *list);
int getBucketIndex(int value);

// função da ordenação
void BucketSort(int arr[]) {
  int i, j;
  struct Node **buckets;

  // criação de balde e alocar tamanho de memória
  buckets = (struct Node **)malloc(sizeof(struct Node *) * NBUCKET);

  // iniciar balde vazio
  for (i = 0; i < NBUCKET; ++i) {
    buckets[i] = NULL;
  }

  // encher o balde com seus respectivos elementos
  for (i = 0; i < NARRAY; ++i) {
    struct Node *current;
    int pos = getBucketIndex(arr[i]);
    current = (struct Node *)malloc(sizeof(struct Node));
    current->data = arr[i];
    current->next = buckets[pos];
    buckets[pos] = current;
  }

  // imprimir baldes com seus elementos
  for (i = 0; i < NBUCKET; i++) {
    printf("Balde[%d]: ", i);
    printBuckets(buckets[i]);
    printf("\n");
  }

  // ordenar os elementos de cada balde
  for (i = 0; i < NBUCKET; ++i) {
    buckets[i] = InsertionSort(buckets[i]);
  }

  printf("-------------\n");
  printf("Baldes depois de ordenar\n");
  for (i = 0; i < NBUCKET; i++) {
    printf("Balde[%d]: ", i);
    printBuckets(buckets[i]);
    printf("\n");
  }

  // inserir os elementos ordenados no array
  for (j = 0, i = 0; i < NBUCKET; ++i) {
    struct Node *node;
    node = buckets[i];
    while (node) {
      arr[j++] = node->data;
      node = node->next;
    }
  }

  return;
}

// função para ordenar os elementos de cada balde
struct Node *InsertionSort(struct Node *list) {
  struct Node *k, *nodeList;
  if (list == 0 || list->next == 0) {
    return list;
  }

  nodeList = list;
  k = list->next;
  nodeList->next = 0;
  while (k != 0) {
    struct Node *ptr;
    if (nodeList->data > k->data) {
      struct Node *tmp;
      tmp = k;
      k = k->next;
      tmp->next = nodeList;
      nodeList = tmp;
      continue;
    }

    for (ptr = nodeList; ptr->next != 0; ptr = ptr->next) {
      if (ptr->next->data > k->data)
        break;
    }

    if (ptr->next != 0) {
      struct Node *tmp;
      tmp = k;
      k = k->next;
      tmp->next = ptr->next;
      ptr->next = tmp;
      continue;
    } else {
      ptr->next = k;
      k = k->next;
      ptr->next->next = 0;
      continue;
    }
  }
  return nodeList;
}

int getBucketIndex(int value) {
  return value / INTERVAL;
}

void print(int ar[]) {
  int i;
  for (i = 0; i < NARRAY; ++i) {
    printf("%d ", ar[i]);
  }
  printf("\n");
}

// imprimir os baldes
void printBuckets(struct Node *list) {
  struct Node *cur = list;
  while (cur) {
    printf("%d ", cur->data);
    cur = cur->next;
  }
}


int main(void) {
  int array[NARRAY] = {42, 32, 33, 52, 37, 47, 51};

  printf("Array inicial: ");
  print(array);
  printf("-------------\n");

  BucketSort(array);
  printf("-------------\n");
  printf("Array ordenado: ");
  print(array);
  return 0;
}

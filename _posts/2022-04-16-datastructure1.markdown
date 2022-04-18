---

layout: post
title: 자료구조 1장 - Unsorted List & Sorted List
date: 2022-04-16 10:32:23 +0900
category: DataStructure
---
# Unsorted List & Sorted List
c++에서 배열(array)이라는 자료형을 자주 사용했다.
하지만 c++에서는 배열의 크기를 따로 계산하지 않고, index check도 하지 않아서 out of range도 발생하고 불편한 점이 많다.

그래서 기존의 배열에서 좀 더 오류가 없도록, 길이도 계속 계산할 수 있도록 편리하게 하는 자료형을 직접 만들 수 있다.
바로 c++의 객체지향성을 이용해 class로 자료형을 직접 만드는 것이다.

## Unsorted List

Unsorted List는 정렬되지 않은 리스트이다.
기존의 array처럼 사용자가 1, 5, 3, 4, 2라는 데이터를 집어넣으면, 그대로 1, 5, 3, 4, 2로 데이터가 저장된다.
그리고 이 Unsorted List의 길이는 5가 된다. 데이터를 5개 집어 넣었기 때문이다.
우선 ItemType이라는 class가 따로 있다.
나중에 배울 nodeType으로 데이터끼리 연결을 할 것인데 그 개념을 사용하기 위해 비슷한 개념인 ItemType을 쓰도록 하겠다.

ItemType은 또 다른 하나의 자료형이다. List안의 데이터 하나의 자료형이라고 보면 된다.
ItemType의 멤버함수로는 ItemType끼리 크기를 비교하는 CompareTo(), 출력을 하는 Print(), 값을 정해주는 Initialize()가 있다.

> unsorted.h

```c++
#include "ItemType.h" 
class UnsortedType 
{
public:
  UnsortedType();
  bool IsFull() const;
  int LengthIs() const;
  void RetrieveItem(ItemType& item, bool& found);
  void InsertItem(ItemType item);
  void DeleteItem(ItemType item);
  void DeleteItem_a(ItemType item);
  void DeleteItem_b(ItemType item);
  void ResetList();
  void GetNextItem(ItemType& item);
private:
  int length;
  ItemType info[MAX_ITEMS];
  int currentPos;
};
```
> unsorted.cpp

```c++
#include "unsorted.h"
UnsortedType::UnsortedType()
{
  length = 0;
}

bool UnsortedType::IsFull() const
{
  return (length == MAX_ITEMS);
}

int UnsortedType::LengthIs() const
{
  return length;
}

void UnsortedType::RetrieveItem(ItemType& item, bool& found)
{
  bool moreToSearch;
  int location = 0;
  found = false;

  moreToSearch = (location < length);

  while (moreToSearch && !found) 
  {
    switch (item.ComparedTo(info[location]))
    {
      case LESS    : 
      case GREATER : location++;
                     moreToSearch = (location < length);
                     break;
      case EQUAL   : found = true;
                     item = info[location];
                     break;
    }
  }
}

void UnsortedType::InsertItem(ItemType item)
{
  info[length] = item;
  length++;
}

void UnsortedType::DeleteItem(ItemType item)
{
  int location = 0;

  while (item.ComparedTo(info[location]) != EQUAL)
    location++;

  info[location] = info[length - 1];
  length--;
}

void UnsortedType::ResetList()
{
  currentPos = -1;
}

void UnsortedType::GetNextItem(ItemType& item)
{
  currentPos++;
  item = info[currentPos];
}

```

멤버 변수가 length, currentPos, info[MAX_ITEMS]가 있다.
length는 UnsortedList의 길이, info[MAX_ITEMS]는 이 리스트의 데이터를 실제로 저장하는 공간이다.
MAX_ITEMS는 ItemType에서 전역변수로 5를 지정해놓았다. (UnsortedList의 최대 크기가 5라는 의미)

InsertItem()은 item을 class의 멤버변수인 info[MAX_ITEMS]에 저장하는 함수이고,
DeleteItem()은 index를 찾아서 맨뒤의 아이템을 해당 index로 당겨오고 길이를 1줄이는 방법이다.
어차피 info의 데이터 순서는 상관이 없으므로 효율성있는 알고리즘이다.
<img src="../public/img/posts/Datastructure1-deleteitem1.png" />

currentPos는 List에서 어느 index를 가르키고 있는지를 의미한다.
GetNextItem에서 currentPos를 1씩 늘리며 다음 List의 ItemType을 가르키게 된다.
그리고 ResetList()에서 다시 -1로 정의되며 초기화가 된다. (최초는 -1의 상태)

## Sorted List

Sorted List는 Unsorted List에서 정렬 기능이 추가된 자료형이다.
실제 모습은 비슷하지만 정렬이 되어있어서 멤버 함수의 구성이 다르다.

> Sorted.cpp InsterItem

```c++
void SortedType::InsertItem(ItemType item) 
{
  bool moreToSearch;
  int location = 0;

  moreToSearch = (location < length);
  while (moreToSearch) 
  {
    switch (item.ComparedTo(info[location])) 
    {
      case LESS    : moreToSearch = false;
                     break;
      case GREATER : location++;
                     moreToSearch = (location < length);
                     break;
    } 
  } 
  for (int index = length; index > location; index--)
    info[index] = info[index - 1];
  info[location] = item;
  length++;
}
```

Unsorted에서는 순서에 상관이 없어서 InsterItem()의 구현이 단순했지만 Sorted는 크기에 순서가 정해져있다.
그래서 데이터를 넣을 때, item과 순회하는 index에 해당하는 info[location]의 크기를 비교해서 데이터를 넣는다.

> Sorted.cpp DeleteItem

``````
void SortedType::DeleteItem(ItemType item) 
{
  int location = 0;

  while (item.ComparedTo(info[location]) != EQUAL)
    location++;
  for (int index = location + 1; index < length; index++)
    info[index - 1] = info[index];
  length--;
}
``````

DeleteItem()도 unsorted에선 효율성을 추구하기 위해 맨뒤의 아이템을 당겨오는 형식이었지만,
Sorted에서는 순서가 중요하기 때문에 순서를 유지해야하므로 지울 데이터의 뒤 info는 한 칸씩 모두 당겨와야한다.
![](C:\Users\bigto\OneDrive - 경희대학교\바탕 화면\MyProject\myblog\public\img\posts\Datastructure1-deleteitem2.png)



RetrieveItem()은 item이 list에 있는지 없는지 확인하는 함수이다.
이진탐색(Binary Search)알고리즘을 사용할 것이기 때문에 2장에서 설명하겠다.
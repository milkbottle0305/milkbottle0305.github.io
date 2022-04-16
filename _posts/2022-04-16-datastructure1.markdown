---

layout: post
title: 자료구조 1장 - Unsorted List & Sorted List
date: 2022-04-16 10:32:23 +0900
category: DataStructure
---
# Unsorted List & Sorted List
c++에서 배열(array)이라는 자료형을 자주 사용했다. 하지만 c++에서는 배열의 크기를 따로 계산하지 않고, index check도 하지 않아서 out of range도 발생하고 불편한 점이 많다.

그래서 기존의 배열에서 좀 더 오류가 없도록, 길이도 계속 계산할 수 있도록 편리하게 하는 자료형을 직접 만들 수 있다. 바로 c++의 객체지향성을 이용해 class로 자료형을 직접 만드는 것이다.

## Unsorted List

Unsorted List는 정렬되지 않은 리스트이다. 기존의 array처럼 사용자가 1, 5, 3, 4, 2라는 데이터를 집어넣으면, 그대로 1, 5, 3, 4, 2로 데이터가 저장된다. 그리고 이 Unsorted List의 길이는 5가 된다. 데이터를 5개 집어 넣었기 때문이다.

우선 ItemType이라는 class가 따로 있다. 나중에 배울 nodeType으로 데이터끼리 연결을 할 것인데 그 개념을 사용하기 위해 비슷한 개념인 ItemType을 쓰도록 하겠다.

ItemType은 또 다른 하나의 자료형이다. List안의 데이터 하나의 자료형이라고 보면 된다. ItemType의 멤버함수로는 ItemType끼리 크기를 비교하는 CompareTo(), 출력을 하는 Print(), 값을 정해주는 Initialize()가 있다.

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

멤버 변수가 length, currentPos, info[MAX_ITEMS]가 있다. length는 UnsortedList의 길이, info[MAX_ITEMS]는 이 리스트의 데이터를 실제로 저장하는 공간이다. MAX_ITEMS는 ItemType에서 전역변수로 5를 지정해놓았다. (UnsortedList의 최대 크기가 5라는 의미)

currentPos는 List에서 어느 index를 가르키고 있는지를 의미한다. GetNextItem에서 currentPos를 1씩 늘리며 다음 List의 ItemType을 가르키게 된다. 그리고 ResetList()에서 다시 -1로 정의되며 초기화가 된다. (최초는 -1의 상태)

## Sorted List

Sorted List는 Unsorted List에서 정렬 기능이 추가된 자료형이다. 나머진 완전 똑같고 새로운 데이터를 추가하는 InsertItem() 멤버 변수에서만 정렬되게 추가하면 끝이다.

> InsterItem()

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
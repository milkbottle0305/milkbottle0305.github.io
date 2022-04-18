---

layout: post
title: 자료구조 2장 - BinarySearch
date: 2022-04-18 22:34:48 +0900
category: DataStructure
---
# BinarySearch
이 개념은 1장에서 설명한 Unsorted List와 Sorted List의 RetriveItem()함수를 토대로 설명을 진행한다.

만약 우리가 List에서 특정한 Item을 검색한다면 어떻게 찾을 수 있을까?
List의 데이터하나하나를 0부터 length-1까지 검색해서 같은지 다른지 확인해야할 것이다.
즉, 이렇게 하나씩 비교하는 알고리즘의 O(N) 이 된다.

## O(N)인 탐색

> 하나하나씩 비교하는 RetrieveItem()

``````
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
      case LESS    : location++;
                     moreToSearch = (location < length);
                     break;
      case GREATER : location++;
                     moreToSearch = (location < length);
                     break;
      case EQUAL   : found = true;
                     item = info[location];
                     break;
    }
  }
``````

location = 0으로 선언하고 length직전까지 리스트를 순회한다.
ItemType의 멤버함수인 CompareTo()를 통해 데이터가 작은지 큰지 같은지를 비교해서 같다면 while문을 탈출하고 데이터를 찾았다는 의미인 found = ture를 한다.

## O(logN)인 탐색 (Binary Search)

> 검색할 구간을 반으로 나누는 탐색

``````
void SortedType::RetrieveItem(ItemType& item, bool& found) 
{
  int midPoint;
  int first = 0;
  int last = length - 1;

  bool moreToSearch = first <= last;
  found = false;
   while (moreToSearch && !found) 
  {
    midPoint = ( first + last) / 2;
    switch (item.ComparedTo(info[midPoint])) 
    {
      case LESS    : last = midPoint - 1;
                     moreToSearch = first <= last;
                     break;
      case GREATER : first = midPoint + 1;
                     moreToSearch = first <= last;
                     break;
      case EQUAL   : found = true;
                     item = info[midPoint];
                     break;
    }
  }
}
``````

처음은 중간지점을 찍어서 그 값이 같다면 찾은 것이고, 크다면 first를 당겨오고 작다면 last를 당겨와서 계속 찾는지점을 줄이는 알고리즘이다.

![](..\public\img\posts\Datastructure2-BinarySearch.png)

first=9, last=6이고 midPoint는 first와 last의 중간지점이다. 처음은 midPoint인 6과 9를 비교해서 9가 6보다 크므로 first를 midPoint의 뒤로 당겨온다.

두 번째에는 midPoint가 5번째 인덱스인 10이 되므로 찾을 9가 10보다 작으므로 last를 midPoint의 앞으로 당겨온다.

마지막인 세 번째에는 어차피 남은게 9이므로 9를 바로 찾을 수 있다.

기존의 O(N)인 방법으로 찾았다면 9는 4번째 index에 있으므로 5번을 비교해야하지만, Binary Search으로는 3번만에 찾았다.
Binary Search의 시간복잡도는 O(logN)이다.
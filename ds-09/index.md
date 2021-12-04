# [자료구조] 트라이


비선형적인 자료구조 트라이에 대해 정리

<!--more-->

## 트라이
- 검색 트리의 일종으로 일반적으로 키가 문자열인 동적 배열 또는 연관 배열을 저장하는데 사용되는 정렬된 트리 자료구조

## 트라이 구현 (python)

```python
import collections

class TrieNode:
    def __init__(self):
        self.word = False
        self.children = collections.defaultdict(TrieNode)

class Trie:
    def __init__(self):
        self.root = TrieNode()

    def insert(self, word):
        node = self.root
        for char in word:
            node = node.children[char]
        node.word = True
    
    def search(self, word):
        node = self.root
        for char in word:
            if char not in node.children:
                return False
            node = node.children[char]
        return node.word

    def startWith(self, prefix):
        node = self.node
        for char in prefix: 
            if char not in node.children:
                return False
            node = node.children[char]

        return True
```

## Reference
- 파이썬 알고리즘 인터뷰 (박상길 지음)

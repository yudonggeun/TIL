[2022-05-06] [게임 맵 최단거리 문제 풀이](https://programmers.co.kr/learn/courses/30/lessons/1844#qna)
* 잘못된 완료 조건
```java
public int findMinPath(){
        //초기화
        Queue<int[]> queue = new LinkedList<int[]>();
        int count = 0;
        queue.add(new int[]{0, 0, 1});
        isVisit[0][0] = true;
        //탐색 시작
        while(!queue.isEmpty()){
            int[] sp = queue.poll();
    
            if(sp[0] == rowMax && sp[1] == colMax)//실수한 부분
                return sp[2];
    
            for(int i = 0; i< 4; i++){
                int nR = sp[0] + mRow[i];
                int nC = sp[1] + mCol[i];
                int nCount = sp[2] + 1;
        
                if(!isRoad(nR, nC)) continue;
                if(isVisit[nR][nC]) continue;
        
                isVisit[nR][nC] = true;
                queue.add(new int[]{nR, nC, nCount});
            }
        }
        return -1;
} 
```
문제의 조건을 좀 더 확실하게 살펴보기
<br>

* 
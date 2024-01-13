# 24_01_13_daily_certification

```
[koreii] #13 데일리인증
20240111
알고리즘
- HashMap, HashSet과 DFS를 활용한 복합적인 문제 풀이 + 난이도 기여
```

# Problem Solving (Algorithm & SQL)

**BOJ 22860 폴더 정리 (small)**

[22860번: 폴더 정리 (small)](https://www.acmicpc.net/problem/22860)

File(폴더, 폴더가 아닌 파일 모두 포함) 클래스를 정의한다.

파일명, 폴더명이 고유하므로(문제 조건을 잘 읽어보면 알수 있다.) 파일명을 key로 Int형 id를 부여받아서 index로 사용할 수 있다.

한 종류의 파일이 하나의 폴더에 여러 번 들어갈 수도 있고, 여러 폴더에 포함될 수도 있다. 종류는 중복해서 세지 않지만 개수는 여러 번 세야 한다는 점에 주의한다.

File 객체가 폴더가 아닐 경우 하위 파일 개수를 1로 하고 DFS로 탐색하여 리프 노드부터(엄밀히 말하면 트리 구조가 아닐 수 있으므로 리프 노드는 아니다.) 하위 파일 개수를 정해서 루트(마찬가지)로 올라간다.

파일 개수는 DFS의 반환값을 활용하고 파일 종류 개수는 각 객체에 HashSet을 정의해서 구한다.

**코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_22860 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private val fileMap = HashMap<String, Int>()
    private var id = 1
    private val fileList = Array<File>(2002) { File(false) }

    private var n = 0   //  폴더 개수
    private var m = 0   //  파일 개수
    private var q = 0   //  쿼리 개수

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        n = tokens.nextToken().toInt()
        m = tokens.nextToken().toInt()

        fileList[0] = File(true)
        fileMap["main"] = 0

        repeat(n + m) {
            tokens = StringTokenizer(br.readLine())

            val parentFolder = tokens.nextToken()
            val childFile = tokens.nextToken()
            val isFolder = tokens.nextToken().toInt() == 1

            var parentId = -1
            var childId = -1

            if(fileMap.contains(parentFolder))  //  이미 나온 적 있는 폴더일 경우
                parentId = fileMap[parentFolder]!!
            else {  //  처음 등장하는 폴더일 경우
                parentId = id++
                fileMap[parentFolder] = parentId
                fileList[parentId] = File(true)
            }

            if(fileMap.contains(childFile)) //  이미 나온 적 있는 폴더/파일일 경우
                childId = fileMap[childFile]!!
            else {
                childId = id++
                fileMap[childFile] = childId
                fileList[childId] = File(isFolder)

                if(!isFolder) {
                    fileList[childId].fileCnt = 1
                    fileList[childId].fileSet.add(childFile)
                }
            }

            fileList[parentId].children.add(childId)
        }

        dfs(0)

        q = br.readLine().toInt()

        repeat(q) {
            val split = br.readLine().split("/")
            val targetFolder = split[split.size - 1]
            val targetFolderId = fileMap[targetFolder]!!

            sb.append("${fileList[targetFolderId].fileSet.size} ${fileList[targetFolderId].fileCnt}\n")
        }

        print(sb)
    }

    private fun dfs(fileId : Int) : Int {
        if(fileList[fileId].isFolder) {
            for (childId in fileList[fileId].children) {
                fileList[fileId].fileCnt += dfs(childId)

                for(childFileName in fileList[childId].fileSet)
                    fileList[fileId].fileSet.add(childFileName)
            }
        }

        return fileList[fileId].fileCnt
    }

    private class File(val isFolder : Boolean) {
        var fileCnt = 0     //  하위 파일 개수 (폴더는 미포함)
        val children = ArrayList<Int>() //  하위 파일 id 저장 리스트
        val fileSet = HashSet<String>() //  하위 파일 종류
    }
}

fun main() {
    BOJ_22860().solve()
}
```
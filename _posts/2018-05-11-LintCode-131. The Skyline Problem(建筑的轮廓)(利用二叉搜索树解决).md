##LintCode-131. The Skyline Problem(建筑的轮廓)(利用二叉搜索树解决)
###[题目链接](https://www.lintcode.com/problem/the-skyline-problem/description)
###题意: 
![这里写图片描述](https://img-blog.csdn.net/20180815150347423?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p4enh6eDAxMTk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
###解析: 
解析过程:

 - 将输入的每一组数据处理成两个Node结构，结构中是[位置，高度，上升/下降]的信息；
 - 对上面的Node结构数组按照位置进行升序排序；
 - 准备两个TreeMap，第一个htMap存[高度，次数]，第二个pmMap存[位置，最大高度] (注意两个Map都是按照key有序的)；
 - 遍历Node数组，如果是上升且没有出现目前的，就加入一个(1次)，如果出现过，就++；
 - 如果是下降且只有一个了，就移除，否则- -；
 - 最后要把每个位置的最大高度存到pmMap中；
 - 最后使用pmMap构造出答案，因为记录了每个位置的最大高度变化，只要前一个高度和当前高度不同，就构造出来；

![这里写图片描述](https://img-blog.csdn.net/20180815150728808?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p4enh6eDAxMTk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

```
import java.util.*;
/**
 * 大楼的轮廓线问题
 */
public class LintCode131_TheSkylineProblem {


    //由一座大楼可以生成两个信息，一个是开始，高度，和上升
    private static class Node{
        public int pos; //position
        public int h; //height
        public boolean isUp;

        public Node(int pos, int h, boolean isUp) {
            this.pos = pos;
            this.h = h;
            this.isUp = isUp;
        }
    }

    //sorted by start nod,  if repeat,sorted by isUp
    private static class NodeComparator implements Comparator<Node> {

        @Override
        public int compare(Node o1, Node o2) {
            if(o1.pos != o2.pos){
                return o1.pos - o2.pos; //ascend by pos
            }
            //the same position
            if(o1.isUp != o2.isUp){ //相同的位置下, 上的排在前面
                return o1.isUp ? -1 : 1;
            }
            return 0;
        }
    }
    
    public List<List<Integer>> buildingOutline(int[][] buildings) {
        Node[] node = new Node[2 * buildings.length];     // each building to two message
        for(int i = 0; i < buildings.length; i++){
            node[i * 2] = new Node(buildings[i][0],buildings[i][2],true); //up
            node[i * 2 + 1] = new Node(buildings[i][1],buildings[i][2],false);// down
        }
        Arrays.sort(node,new NodeComparator()); //sorted by start
        TreeMap<Integer,Integer>htMap = new TreeMap<>(); // key : height ; value : times
        TreeMap<Integer,Integer>pmMap = new TreeMap<>(); // key : pos(every) ; value : maxHeight
        for(int i = 0; i < node.length; i++){
            if(node[i].isUp){  //if it's up
               if(!htMap.containsKey(node[i].h)){
                   htMap.put(node[i].h,1);
               }else {
                   htMap.put(node[i].h,htMap.get(node[i].h) + 1); //add the times
               }
            }else { // down
                if(!htMap.containsKey(node[i].h))continue;
                if(htMap.get(node[i].h) == 1) {
                    htMap.remove(node[i].h);
                }else {
                    htMap.put(node[i].h,htMap.get(node[i].h) - 1);
                }
            }

            if(htMap.isEmpty()){
                pmMap.put(node[i].pos,0);
            }else {
                pmMap.put(node[i].pos,htMap.lastKey()); //存入当前位置和的当前的最大高度
            }
        }

        //根据pmMap 构造出轮廓
        List<List<Integer>>res = new ArrayList<>();
        int start = 0,height = 0; //一开始 = 0
        for(Map.Entry<Integer,Integer>entry : pmMap.entrySet()){
            int curPosition = entry.getKey();
            int maxHeight = entry.getValue();

            if(height != maxHeight){  //发现改变  有轮廓
                if(height != 0){  //这个是第一个要判断一下
                    List<Integer>temp = new ArrayList<>();
                    temp.add(start); //起点
                    temp.add(curPosition); //当前的
                    temp.add(height);
                    res.add(temp);
                }
                start = curPosition;
                height = maxHeight;
            }
        }
        return res;
    }

    public static void main(String[] args) {
        int[][] buildings = {
                {1, 3, 3},
                {2, 4, 4},
                {5, 6, 1}
        };

        System.out.println(new LintCode131_TheSkylineProblem().buildingOutline(buildings));
    }
}
```
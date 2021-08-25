```java
import java.io.*;
import java.util.*;
import java.util.regex.Pattern;

public class Main {

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

        String[] fruitWeights = br.readLine().replaceAll("fruitWeights: \\[","").replaceAll("]","").split(" ");
        int [] w = new int[fruitWeights.length];
        for(int i=0;i< fruitWeights.length;i++){
            w[i] = Integer.parseInt(fruitWeights[i]);
        }
        String k = br.readLine().replaceAll("k: ","");

        solution(w,Integer.parseInt(k));
    }

    private static void solution(int[] w, int k) {
        List<Integer> maxGroup = new ArrayList<Integer>();
        for(int i=0;i<w.length-k+1;i++){
            int max = 0;
            for(int j=0;j<k;j++){
                max = max < w[j+i] ? w[j+i]:max;
            }
            if(!maxGroup.contains(max))
                maxGroup.add(max);
        }
        maxGroup.sort(Comparator.reverseOrder());
        System.out.println(maxGroup);
    }
}
```

```java
import java.io.*;
import java.util.*;
import java.util.regex.Pattern;

public class Main {
    public static int m,n;

    public static List<Integer> list = new LinkedList<>();
    public static boolean hold = false; //true면 몇일동안 못나감
    public static int holdDays = 0; //몇일째 잠금인지

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        String [] mn = br.readLine().split(" ");

        m = Integer.parseInt(mn[0]);
        n = Integer.parseInt(mn[1]);

        for(int i=0;i<m;i++){
            list.add(0);
        }

        while(true){
            String command = br.readLine();
            if("EXIT".equals(command)){
                System.out.println("BYE");
                break;
            }
            switch (command){
                case "SHOW":
                    //linkedlist 개수의 합이 n 미만이거나 hold 상태가 아닐때
                    if(checkList()) {
                        System.out.println("1");
                        list.set(list.size()-1, list.get(list.size()-1)+1);
                    }else {
                        System.out.println("0");
                    }
                    break;
                case "NEGATIVE":
                    hold = true;
                    holdDays=0;
                    System.out.println("0");
                    break;
                case "NEXT":
                    if(hold){
                        holdDays++;
                    }
                    if(holdDays == m+1){
                        hold = false;
                        holdDays=0;
                    }
                    list.add(0);
                    System.out.println("-");
                    break;
                default:
                    System.out.println("ERROR");
            }
        }
    }

    public static boolean checkList(){
        if(hold){
            return false;
        }
        int sum=0;
        for(int i=0;i<m;i++){
            sum += list.get(list.size()-i-1);
        }
        if(sum>=n){
            hold = true;
            return false;
        }
        return true;
    }
}
```

```java
import java.io.*;
import java.util.ArrayList;
import java.util.List;
import java.util.Stack;
import java.util.StringTokenizer;

public class Main {
    public static void main(String[] args) throws IOException {

        //solution(orderAmount, taxFreeAmount, serviceFee)
        System.out.println(solution(10000, 1000, 10));
    }

    /*
    * 부가가치세를 리턴해야함
    * VAT =  부가가치세
    * supplyPrice = 공급과액
    * VAT = 과세금액 * 0.1
    * 과세금액 = 공급과액 - taxFreeAmount
    * orderAmount - serviceFree= 공급과액 + VAT */
    public static double solution(int orderAmount, int taxFreeAmount, int serviceFree){
        //부가가치세
        double VAT = 0;
        if(orderAmount- serviceFree- taxFreeAmount == 1){
            return 0;
        }
        VAT = Math.round((orderAmount-taxFreeAmount-serviceFree)/11);
        return VAT;
    }

}
```

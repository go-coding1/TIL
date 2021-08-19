```java
import java.io.*;
import java.util.*;

public class Main {
    public static void main(String[] args) throws IOException {
        /*
        int servers = 2;
        boolean sticky = true;
        List requests = Arrays.asList(1,2,2,3,4,1);

        int servers = 2;
        boolean sticky = true;
        List requests = Arrays.asList(1,1,2,2);
        */

        int servers = 2;
        boolean sticky = false;
        List requests = Arrays.asList(1,2,3,4);

        List result = solution(servers, sticky, requests);


        System.out.println(result);
    }

    private static List solution(int servers, boolean sticky, List requests) {
        List<List> server = new ArrayList<>();
        //servers갯수 만큼 서버 생성 완료
        for(int i=0;i<servers;i++){
            server.add(new ArrayList());
        }
        //request가 들어가야할 서버의 번호를 저장하는 해쉬맵
        Map<Integer, Integer> serverInfo = new HashMap<Integer, Integer>();

        int beforeServerInfo = -1;
        for(int i=0;i<requests.size();i++){
            int request = (Integer)requests.get(i);
            if(!sticky) {
                server.get(i % servers).add(request);
            }else{
                int serverNum = serverInfo.getOrDefault(request,-1);
                if(serverNum == -1) {    //처음 들어가는 번호일때

                    if(beforeServerInfo==-1) {
                        serverInfo.put(request, 0);
                        server.get(0).add(request);
                        beforeServerInfo = 0;
                    }else {
                        serverInfo.put(request, (beforeServerInfo+1)%servers);
                        server.get((beforeServerInfo+1) % servers).add(request);
                        beforeServerInfo = (beforeServerInfo+1) % servers;
                    }
                }else{
                    server.get(serverNum).add(request);
                    beforeServerInfo=serverNum;
                }
            }
        }

        return server;
    }

}
```

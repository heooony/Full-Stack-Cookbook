# MVC 구조 실습

본격적으로 MVC구조에 대한 실습을 해보려고 한다.
이 구조에 대해 이전 Post에서도 나름 작성한 것을 게시해보았지만,
구조에 대한 미흡한 부분도 많고, 조금 더 뚜렷하게 해보자는 마음으로 새롭게 구현해보려고 한다.

다음 사진은 잘 작아서 보이지 않겠지만, MVC구조를 가지고 interface와 사용자 정의 exception을 포함하여
구현하려는 전체적인 내용이다.

![boardClassDiagram](https://user-images.githubusercontent.com/68142821/108051836-b2a8b580-708e-11eb-95f1-c753abeff4ee.png)

## View

View는 MVC구조에서 사용자와 밀접하게 지내야 하는 부분을 의미한다.
우리가 UI로 보는 어플리케이션이나, 웹 뷰 등 서비스를 사용하는 사람들은 내부적인 구조를 알아가면서 사용하는 것이 아니다.

즉, 코딩을 하나도 모르는 사람들에게 우리는 선택권을 주면서, 그 선택에 대한 보장을 해주어야 한다는 것이다. 잘 이해가 가지 않는다면 이것을 나름대로 표현해보겠다.
ㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡ
1. 학생 정보 추가
2. 학생 정보 제거
3. 학생 리스트 출력
4. 종료

ㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡ

다음 중 숫자를 입력하는 것에서 내부적인 코드를 알아야 할까?
당연히 아닐 것이다. 우리는 사용자로부터 직접 원하는 입력을 받을 수 있도록 노력하는 것 뿐이다.
그러나 이번 실습에서는 사용자로부터 직접 입력 받는 것이 아닌, 임의의 값으로 실험할 수 있도록 해보았다.

### MainView.java
```java
import kosta.exam.controller.BoardController;
import kosta.exam.dto.ArchiveBoard;
import kosta.exam.dto.PhotoBoard;

public class MainView {

    public static void main(String[] args) {
        //1.archive와 photo 모든 게시물 검색
        System.out.println("1. 모든 게시물 검색");
        BoardController.getAllBoard();
        
        
        System.out.println("\n");
        
        //2. archive 또는 photo  게시판에 있는 모든 게시물 검색
        System.out.println("2. 게시판 유형에 따른 검색");  
        
        System.out.println("2-1.게시판유형이 존재하는 경우.....");
        BoardController.getBoardByKind("archive");
        
        BoardController.getBoardByKind("photo");
        
        System.out.println("2-2.게시판유형이 없는 경우.....");
        BoardController.getBoardByKind("test");
        
        
        System.out.println("\n");
        
        /*3.글번호에 해당하는 자료실검색
         존재하는 게시물 검색할 경우와 없는 게시물 검색 각각 test 결과 출력하기*/
         
        System.out.println("3.Board에서 글번호에 해당하는 게시물 검색");
        System.out.println("-----게시물이 존재하는 경우 검색 test----- ");
        BoardController.getBoardByNo("archive", 10); //존재하는 게시물
        
        System.out.println();
        
        System.out.println("-----게시물이 없는 경우(글번호 오류)  검색 test-----");
        BoardController.getBoardByNo("archive", 50);// 없는 게시물
        
                System.out.println();
        
        System.out.println("-----게시물이 없는 경우(kind오류)  검색 test-----");
        BoardController.getBoardByNo("test", 10);// 없는 게시물
        
        System.out.println("\n");
        
        
        //4. 등록하기
        System.out.println("--4-1. 등록하기(성공!!)");
        BoardController.insertBoard("photo" ,
                new PhotoBoard(40, "mvc공부", "장희정111", "재미있다.", "2018-08-18", "a.jpg"));
        
        System.out.println("--4-2. 등록하기(실패!! - 글번호 중복!)");
        BoardController.insertBoard("photo" ,
                new PhotoBoard(10, "mvc공부", "장희정", "재미있다.", "2018-08-18", "a.jpg"));
        
        System.out.println("--4-2. 등록하기(실패!! - kind 잘못!!!!)");
        BoardController.insertBoard("photo2" ,
                new PhotoBoard(50, "mvc공부", "장희정", "재미있다.", "2018-08-18", "a.jpg"));
        
        
        System.out.println("---검색하기 --------");
        BoardController.getAllBoard();
        
        System.out.println("--5. 삭제하기..............");
        System.out.println("---5-1. 삭제성공하기-----------------");
                BoardController.deleteBoard("photo", 20);
        
        
                System.out.println("---5-2. 삭제실패하기(kind 오류) -----------------");
                BoardController.deleteBoard("test", 20);
        
        
                System.out.println("---5-3. 삭제실패하기(글번호 오류) -----------------");
                BoardController.deleteBoard("photo", 70);
        
                System.out.println("---검색하기 --------");
        BoardController.getAllBoard();
        
        System.out.println("---6.수정하기----------------");
        System.out.println("--6-1. photo수정 완료-----------------");
        BoardController.updateBoard(new PhotoBoard(10, "점심시간", "이효리", "뭐 먹지??", null, "test.gif") , "photo");
        
        System.out.println("--6-2. archive수정 완료-----------------");
        BoardController.updateBoard(new ArchiveBoard(10, "점심시간", "이효리", "뭐 먹지??", null, "a.xls",300) , "archive");
        
        //게시판유형 잘못
        System.out.println("--6-3. 수정실패(kind 잘못) -----------------");
        BoardController.updateBoard(new ArchiveBoard(10, "점심시간", "이효리", "뭐 먹지??", null, "a.xls",300) , "test");
    
        System.out.println("--6-3. 수정실패(no 잘못) -----------------");
        BoardController.updateBoard(new ArchiveBoard(80, "점심시간", "이효리", "뭐 먹지??", null, "a.xls",300) , "archive");
    
         
                System.out.println("---검색하기 --------");
        BoardController.getAllBoard();
    }
}
```

### SuccessView.java
성공적인 출력이 예상된다면 다음 View를 이용하여 출력할 수 있도록 한다.
```java
import java.util.Map;

import kosta.exam.dto.Board;

public class SuccessView {
    public SuccessView() {}
    public static void printBoard(Map<String, Map<String, Board>> map) {
        for(Map.Entry<String, Map<String, Board>> keyMap : map.entrySet()) {
            String k = keyMap.getKey();
            System.out.println("====" + k + "의 모든 게시물 ====");
            Map<String, Board> v = keyMap.getValue();
            for(Map.Entry<String, Board> valueMap : v.entrySet()) {
                String key = valueMap.getKey();
                Board value = valueMap.getValue();
                System.out.println("key = " +  key + " [ " + value + " ] ");
            }
        }
    }
    
    public static void printBoardByKind(String kind, Map<String, Board> map) {
        System.out.println("==== " + kind + "유형 게시물 List ====");
        for(Map.Entry<String, Board> printMap : map.entrySet()) {
            String key = printMap.getKey();
            Board value = printMap.getValue();
            System.out.println("번호 : " + key + ", " + value);
        }
    }
    
    public static void printBoardByNo(Board board) {
        System.out.println(board);
    }
    
    public static void printMessage(String message) {
        System.out.println(message);
    }
}
```

### FailView.java
사용자의 선택이 실패로 예상된다면 다음 View를 이용하여 출력할 수 있도록 한다.
```java
public class FailView {
    FailView() {}
    public static void errorMessage(String message) {
        System.out.println(message);
    }
}
```

## Exception
이번 실습에서는 사용자 정의 Exception을 추가하였다.
InexistException은 존재하지 않는 번호의 사용자 접근을 방지하기 위한 예외이며
DuplicateException은 key값이 중복되지 않도록 유일성을 위한 예외이다.

### InexistException.java
```java
public class InexistentException extends RuntimeException {
    public InexistentException() {}
    public InexistentException(String message) {
        super(message);
    }
}
```

### DuplicateException.java
```java
public class DuplicateException extends RuntimeException {
    public DuplicateException() {}
    public DuplicateException(String message) {
        super(message);
    }
}
```
# MVC 구조 실습


![boardClassDiagram](https://user-images.githubusercontent.com/68142821/108051836-b2a8b580-708e-11eb-95f1-c753abeff4ee.png)

## Model
직접 만들어보면서 이해하게 된 내용은 이렇다.

사용자로부터 입력을 받아 Controller에서 정리를 해주고, Controller는 사용자의 요청을 수행할 수 있는 기능을 가진 장소를 선택한다. 즉, Model은 Controller로부터 받은 요청을 수행할 수 있는 단계이다. 그렇기 때문에 역할을 수행할 수 있는 **모든 정보와 데이터**를 갖고 있어야 한다는 것이다.

또한 실질적인 기능을 수행하는 부분이라 View나 Controller보다 먼저 구현하는 것이 편했다.

나는 Map을 활용해서 게시글을 올리는 연습을 할 것이며,
게시글에 대한 정보는 '이미지를 올릴 수 있는 게시판', '첨부 파일을 올릴 수 있는 게시판'으로 나누어 사용자에게 제공해 줄 것이다.

게시물에 대한 정보를 저장할 수 있는 Class를 Board라고 지정하고,
ArchiveBoard와 PhotoBoard가 Board를 상속할 수 있도록 한다.

### Board.java
```java
public class Board {
    private int no;
    private String subject;
    private String writer;
    private String content;
    private String date;
    
    public Board() {}
    public Board(int no, String subject, String writer, String content, String date) {
        super();
        this.no = no;
        this.subject = subject;
        this.writer = writer;
        this.content = content;
        this.date = date;
    }
    
    public int getNo() {
        return no;
    }
    public void setNo(int no) {
        this.no = no;
    }
    public String getSubject() {
        return subject;
    }
    public void setSubject(String subject) {
        this.subject = subject;
    }
    public String getWriter() {
        return writer;
    }
    public void setWriter(String writer) {
        this.writer = writer;
    }
    public String getContent() {
        return content;
    }
    public void setContent(String content) {
        this.content = content;
    }
    public String getDate() {
        return date;
    }
    public void setDate(String date) {
        this.date = date;
    }
    @Override
    public String toString() {
        return "번호 : " + no + ", 제목 : " + subject +
                ", 작성자 : " + writer + ", 내용 : " + content + ", 날짜 : "+ date;
    }
}
```

### ArchiveBoard.java
```java
public class ArchiveBoard extends Board{
    private String fileName;
    private int fileSize;
    
    public ArchiveBoard() { }
    public ArchiveBoard(int no, String subject, String writer, String content, String date, String fileName, int fileSize) {
        super(no, subject, writer, content, date);
        this.fileName = fileName;
        this.fileSize = fileSize;
    }
    
    public String getFileName() {
        return fileName;
    }
    public void setFileName(String fileName) {
        this.fileName = fileName;
    }
    public int getFileSize() {
        return fileSize;
    }
    public void setFileSize(int fileSize) {
        this.fileSize = fileSize;
    }
    @Override
    public String toString() {
        return super.toString() + ", 첨부파일 이름 : " + fileName + ", 파일 용량 : " + fileSize;
    }
}
```

### PhotoBoard.java
```java
public class PhotoBoard extends Board{
    private String imgName;
    Object obj;
    
    public PhotoBoard() { }
    public PhotoBoard(int no, String subject, String writer, String content, String date, String imgName) {
        super(no, subject, writer, content, date);
        this.imgName = imgName;
    }
    
    public String getImgName() {
        return imgName;
    }
    public void setImgName(String imgName) {
        this.imgName = imgName;
    }
    
    @Override
    public String toString() {
        return super.toString() + ", 이미지 이름 : " + imgName;
    }
}
```

여기까지가 DTO에 대한 작업이라고 볼 수 있겠다.
DTO에 대한 형식은 항상 하는 작업이라 철자만 틀리지 않으면 어렵지 않게 넘어가는 것 같다.

다음은 Service에 대한 부분이다.
이 프로그램에 대한 전체적인 기능을 수행하고, 예외 지정, 던지기 등 가장 머리를 써야 하는 부분이다.
Service는 먼저 interface를 구현하여 앞으로 사용할 메소드를 정의하는 부분을 넣어주었다.
다음은 Service에 대한 부분이다.
이 프로그램에 대한 전체적인 기능을 수행하고, 예외 지정, 던지기 등 가장 머리를 써야 하는 부분이다.
Service는 먼저 interface를 구현하여 앞으로 사용할 메소드를 정의하는 부분을 넣어주었다.
### Service.java(interface)
```java
/**
 * 모든 게시판이 공통으로 사용하게 될 Service
 * (Business Logic)
 * */

import java.util.Map;
import kosta.exam.dto.Board;
import kosta.exam.exception.DuplicateException;
import kosta.exam.exception.InexistentException;

public interface BoardService {
   /**
    *   모든 게시물 검색
    *   @return : 게시판 유형에 따른 모든 게시물 정보를 Map의 형태로 리턴
    *   @throws : 게시물이 한개도 존재 하지 않는다면 InexistentException 예외가
    *             발생한다.
    * */
    Map<String, Map<String, Board>> getBoardList()throws InexistentException;
    
    /**
     *게시판의유형(Kind)에 따른 게시물 검색하기
     *  @return kind에 해당하는 전체게시물 Map으로 리턴
     *  @param :  게시물유형에 따른 key를 인수로 전달받는다.
     *  @throws : kind에 해당하는 게시물이 한개도 없을 때 InexistentException 예외 발생
     * */
    Map<String, Board> getBoardByKind(String kind)throws InexistentException;
    
    /**
     *  게시물 유형에서 글번호에 해당하는 게시물 한개를 검색
     * @return 게시물 정보 한개를 Board 형태로 리턴
     * @param kind는 게시물 유형, no는 게시물 글번호
     * @throws 검색하는 게시물이 없으면 InexistentException 예외발생
     * */
    Board getBoardByNo(String kind, int no)throws InexistentException;
    
    /**
     *  게시물 등록하기
     *  @return : true이면  등록성공, false이면 등록실패
     *  @param : kind는 추가할 게시판유형, board는 추가할 게시물의 정보
     *  @throws: 등록하기 전에 글번호 중복을 체크해서 중복이면 DuplicateException발생
     *           등록을 실패하면  InexistentException 발생
     * */
    boolean insertBoard(String kind, Board board)throws DuplicateException, InexistentException;

        /**
         * 5. 등록전에 번호 중복여부 체크 하는 기능
         *    @return : true이면 중복, false이면 중복아님.
         *    @param : kind의 게시판의 유형, no는 글번호
         * */
    boolean duplicateByNo(String kind, int no);
    
    /**
     * 6. 게시물 삭제하기
     *  @return : true이면 삭제성공, false이면 삭제실패
     *  @param : kind의 게시판의 유형, no는 글번호
     *  @throws : 삭제가 되지 않으면 InexistentException 예외 발생.
     * */
    boolean deleteBoard(String kind, int no)throws InexistentException;
    
    /**
     * 7. 게시물 수정하기
     *     공통 : 제목, 작성자, 내용
     *     photo : imgName
     *     archive : fileName, fileSize
     *     
     * @param : board는 수정할 게시물의 정보, kind는 수정될 게시물이있는 게시판의유형
     * @throws: 수정되지 않으면 InexistentException 예외발생
     * */
    boolean updateBoard(Board board, String kind)throws InexistentException;
}
```

### BoardService.java
생성자 부분을 보면 게시물에 대한 초기 값을 전달해주기 위해 Properties를 활용하여 파일을 로딩하는 부분이 있다.
저번 Map에서 실습할 때에 진행한 것이며, Map을 중점적으로 사용해보기 위해 Properties파일을 제작하여 그 안에 초기 값을 넣어두었다.
```java
import java.util.HashMap;
import java.util.Map;
import java.util.ResourceBundle;
import kosta.exam.dto.ArchiveBoard;
import kosta.exam.dto.Board;
import kosta.exam.dto.PhotoBoard;
import kosta.exam.exception.DuplicateException;
import kosta.exam.exception.InexistentException;

public class BoardServiceImpl implements BoardService {
    Map<String, Map<String, Board>> allBoardList = new HashMap<String, Map<String, Board>>();
    private static BoardServiceImpl instance = new BoardServiceImpl();
    
    private BoardServiceImpl() {
        
        Map<String, Board> amap = new HashMap<String, Board>();
        Map<String, Board> pmap = new HashMap<String, Board>();
        
        //archive 파일 로딩해서 객체로 만들고 Map에 넣기
        ResourceBundle rb = ResourceBundle.getBundle("kosta/exam/service/archiveInfo");
        for(String key : rb.keySet()) {
            String value = rb.getString(key);
            String v [] = value.split(",");
            ArchiveBoard board = new ArchiveBoard(Integer.parseInt(v[0]), v[1], v[2], v[3], v[4], v[5], Integer.parseInt(v[6]));
            amap.put(key, board);
        }
        //photo로딩해서 객체로 만들고 Map에 넣기
        rb = ResourceBundle.getBundle("kosta/exam/service/photoInfo");
        for(String key : rb.keySet()) {
            String value = rb.getString(key);
            String v [] = value.split(",");
            PhotoBoard board = new PhotoBoard(Integer.parseInt(v[0]), v[1], v[2], v[3], v[4], v[5]);
            pmap.put(key, board);
        }
        //위 각각의 Map을 allBoard
        allBoardList.put("archive", amap);
        allBoardList.put("photo", pmap);
    }
    
    public static BoardServiceImpl getInstance() {
        return instance;
    }
    
    @Override
    public Map<String, Map<String, Board>> getBoardList() {
        return allBoardList;
    }

    @Override
    public Map<String, Board> getBoardByKind(String kind) {
        Map<String, Board> map = allBoardList.get(kind);
        if(map == null) {
            throw new InexistentException();
        }
        return map;
    }

    @Override
    public Board getBoardByNo(String kind, int no) {
        Map<String, Board> map = allBoardList.get(kind);
        if(map == null) throw new InexistentException(kind);
        Board board = map.get(Integer.toString(no));
        if(board == null) throw new InexistentException(Integer.toString(no));
        return board;
    }

    @Override
    public boolean insertBoard(String kind, Board board) {
        if(!duplicateByNo(kind, board.getNo())) {
            Map<String, Board> map = new HashMap<String, Board>();
            map.put(Integer.toString(board.getNo()), board);
            allBoardList.get(kind).put(Integer.toString(board.getNo()), board);
            return true;
        } else {
            throw new DuplicateException();
        }
    }

    @Override
    public boolean duplicateByNo(String kind, int no) {
        Map<String, Board> map = allBoardList.get(kind);
        if(map == null) throw new InexistentException();
        Board board = map.get(Integer.toString(no));
        if(board == null) return false;
        return true;
    }

    @Override
    public boolean deleteBoard(String kind, int no) {
        if(duplicateByNo(kind, no)) {
            allBoardList.get(kind).remove(Integer.toString(no));
            return true;
        }
        return false;
    }

    @Override
    public boolean updateBoard(Board board, String kind) {
        if(duplicateByNo(kind, board.getNo())) {
            allBoardList.get(kind).put(Integer.toString(board.getNo()), board);
            return true;
        }
        return false;
    }
}
```

### archiveInfo.properties
무슨 말인지 이해가 안 되겠지만, eclipse에서 properties의 확장자 파일을 제작하여 그 안에 한글을 넣으면 다음과 같이 자동 대입을 해준다.
~~편리한 eclipse~~
```java
#key=no,subject,writer,content,date,fileName,fileSize
10=10,Java\uAD50\uC548\uC790\uB8CC,\uC774\uCC2C\uBC94,\uC790\uBC14\uC218\uC5C5\uC744 \uC6B0\uD55C \uC790\uB8CC,2017-03-27,java.ppt,200
20=20,struts2\uC218\uC5C5,\uC774\uAC00\uD604,struts2\uC218\uC5C5\uC740 \uC5B4\uB5A8\uAE4C??,2017-03-25,struts2.ppt,500
```

### photoInfo.properties
```java
#key=no,subject,writer,content,date,imgName
10=10,kcb\uC778\uD134\uAD50\uC721,\uC7A5\uD76C\uC815,\uBAA8\uB450 \uD654\uC774\uD305!,2017-03-23,love.gif
20=20,\uBCF4\uC548\uAD50\uC721,\uD64D\uAE38\uB3D9,\uC790\uBC14 \uBCF4\uC548\uC774\uB780?,2017-04-01,test.gif
30=30,\uD55C\uAD6D\uB370\uC774\uD130\uBCA0\uC774\uC2A4\uC9C4\uD765\uC6D0,\uC7A5\uD76C\uC815,\uC815\uB9D0 \uC88B\uC740 \uAD50\uC721!!,2017-03-25,girl.gif
```

# MVC 구조 실습

![boardClassDiagram](https://user-images.githubusercontent.com/68142821/108051836-b2a8b580-708e-11eb-95f1-c753abeff4ee.png)

## Controller
우리는 사용자에게 선택권을 주기도, 사용자로부터 입력을 받아 그 기능을 수행하는 부분도 모두 Model과 View로 이루었다.

사용자의 요청이 들어오기 시작할 때부터 그 기능을 수행하기 전까지 **한 구역**을 거쳐야만, 이를 테면 통로 같은 부분이 있어야 정상적으로 수행할 것이다. 나는 Controller을 컴퓨터의 CPU라고 생각한다. 모든 명령의 중앙 처리 장치이며, Brain에 해당하는 역할이기 때문이다.

**Controller**는 사용자의 요청으로 그 기능을 수행해야 하는 메소드를 가리키는 역할을 한다.
이렇게 말하면 정말 작은 역할을 담당하는 것처럼 보이기도 하겠다.

하지만 Model로부터 나오는 모든 예외 처리나 새로운 View를 제공하는 임무, 기능이 성공인지 실패인지 가늠하는 가장 중요한 역할이기도 하다. 위 이미지에서는 하나의 노란색 박스에 불과하지만, **사용자와 개발자를 이어주는 징검다리**같은 역할의 Controller는 program에 있어 매우 중요한 역할을 한다.

### BroadController.java
```java
import java.util.Map;

import kosta.exam.dto.Board;
import kosta.exam.exception.DuplicateException;
import kosta.exam.exception.InexistentException;
import kosta.exam.service.BoardService;
import kosta.exam.service.BoardServiceImpl;
import kosta.exam.view.FailView;
import kosta.exam.view.SuccessView;

public class BoardController {
    private static BoardService boardService = BoardServiceImpl.getInstance();
    public BoardController() {}
    
    public static void getAllBoard() {
        try {
            Map<String, Map<String, Board>> map = boardService.getBoardList();
            if(map == null) {
                throw new InexistentException();
            } else {
                SuccessView.printBoard(map);
            }
        } catch (InexistentException e) {
            FailView.errorMessage("게시물이 존재하지 않습니다.");
        }
    }
    
    public static void getBoardByKind(String kind) {
        try {
            Map<String, Board> map = boardService.getBoardByKind(kind);
            SuccessView.printBoardByKind(kind, map);
        } catch(InexistentException e) {
            FailView.errorMessage("존재하지 않는 유형입니다.");
        }
    }
    
    public static void getBoardByNo(String kind, int no) {
        try {
            Board board = boardService.getBoardByNo(kind, no);
            SuccessView.printBoardByNo(board);
        } catch(InexistentException e) {
            if(e.getMessage().equals(kind))
                FailView.errorMessage("존재하지 않는 유형입니다.");
            else if(e.getMessage().equals(Integer.toString(no))) {
                FailView.errorMessage("존재하지 않는 글 번호입니다.");
            }
        }
    }
    
    public static void insertBoard(String kind, Board board) {
        try {
            if(boardService.insertBoard(kind, board)) {
                SuccessView.printMessage("성공적으로 등록되었습니다.");
            }
        } catch(DuplicateException e) {
            FailView.errorMessage("이미 존재하는 글 번호입니다.");
        } catch (InexistentException e) {
            FailView.errorMessage("존재하지 않는 유형입니다.");
        }
    }
    
    public static void deleteBoard(String kind, int no) {
        try {
            if(boardService.deleteBoard(kind, no)) {
                SuccessView.printMessage("성공적으로 삭제되었습니다.");
            } else {
                FailView.errorMessage("존재하지 않는 글 번호입니다.");
            }
        } catch (InexistentException e) {
            FailView.errorMessage("존재하지 않는 유형입니다.");
        }
    }

    public static void updateBoard(Board board, String kind) {
        try {
            if(boardService.updateBoard(board, kind)) {
                SuccessView.printMessage("성공적으로 수정되었습니다.");
            } else {
                FailView.errorMessage("존재하지 않는 글 번호입니다.");
            }
        } catch (InexistentException e) {
            FailView.errorMessage("존재하지 않는 유형입니다.");
        }
    }
}
```

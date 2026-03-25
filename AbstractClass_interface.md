📌 Abstract Class
- Đặc điểm:
  - Có thể chứa cả phương thức trừu tượng (chưa có thân hàm) và phương thức cụ thể (có thân hàm).
  - Có thể có biến thành viên (fields).
  - Một lớp chỉ kế thừa được một abstract class (do giới hạn của kế thừa đơn trong nhiều ngôn ngữ như Java, C#).
- Khi dùng:
  - Khi muốn chia sẻ logic chung cho nhiều lớp con.
  - Ngoài ra, abstract class có thể chứa biến thành viên và constructor, cho phép chia sẻ logic chung.
  - Khi có một mối quan hệ “is-a” rõ ràng (ví dụ: Dog “is-a” Animal).
- Khi một class kế thừa abstract class:
  - Bắt buộc phải implement các phương thức trừu tượng.
  - Không bắt buộc phải override các phương thức đã có sẵn thân hàm (có thể dùng trực tiếp).

```
abstract class Animal {
    abstract void makeSound(); // phương thức trừu tượng

    void sleep() {             // phương thức cụ thể
        System.out.println("Sleeping...");
    }
}

class Dog extends Animal {
    @Override
    void makeSound() {
        System.out.println("Woof!");
    }
}

public class Main {
    public static void main(String[] args) {
        Dog dog = new Dog();
        dog.makeSound(); // phải implement
        dog.sleep();     // dùng trực tiếp từ abstract class
    }
}
```
📌 Interface
- Đặc điểm:
  - Chỉ định nghĩa hợp đồng (các phương thức, thường không có thân hàm).
  - Không chứa logic thực thi (trừ khi ngôn ngữ hỗ trợ default methods như Java 8).
  - Một lớp có thể implement nhiều interface → hỗ trợ đa kế thừa hành vi.
- Khi dùng:
  - Khi muốn định nghĩa khả năng/hành vi mà lớp có thể thực hiện,là quan hệ “can-do”.
  - Khi cần đảm bảo tính linh hoạt và đa hình.
  - Khi một class đã implements một interface, thì bắt buộc phải viết (implement) toàn bộ các phương thức được khai báo trong interface đó, nếu không trình biên dịch sẽ báo lỗi ngay
- 🔑 Ngoại lệ
  - Nếu class được khai báo là abstract thì có thể chưa cần implement tất cả các phương thức của interface.
  - Nhưng khi có một class cụ thể (concrete class) kế thừa từ nó, thì class đó phải implement đầy đủ.
 
   ==> Tức là :
  1. Khi một abstract class implements một interface, thì nó không bắt buộc phải implement toàn bộ các phương thức trong interface đó. Lý do là vì abstract class vốn dĩ có thể chứa phương thức trừu tượng (prototype), tức là nó có thể “để trống” việc triển khai và giao trách nhiệm lại cho các lớp con.
  2. Nhưng khi một class bình thường (concrete class) extends từ một abstract class(là abtract class đã implement interface) mà abstract class đó đã implements một interface, thì class bình thường này bắt buộc phải implement tất cả các phương thức còn lại trong interface mà abstract class chưa thực hiện.

```
interface Printable {
    void print();
}

interface Scannable {
    void scan();
}

class MultiFunctionPrinter implements Printable, Scannable {
    public void print() {
        System.out.println("Printing document...");
    }
    public void scan() {
        System.out.println("Scanning document...");
    }
}
```





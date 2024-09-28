### Rust 和 C/C++ 都是系統程式語言，但它們之間有幾個關鍵區別，特別是在記憶體安全、並發處理、錯誤處理和所有權模型等方面。以下是一些 Rust 的基本特性與 C/C++ 的不同之處，並透過程式碼實例來說明這些差異：


### 1. 所有權（Ownership）和借用（Borrowing）
Rust 的核心特性是「所有權系統」，這是與 C/C++ 最顯著的差異。 
Rust 透過編譯時的所有權機制，確保記憶體安全，避免了像 C/C++ 中常見的懸垂指標、記憶體洩漏和資料競爭等問題。

#### Rust 範例：

    fn main() {
          let s1 = String::from("hello");
          let s2 = s1; // s1 的所有權轉移給 s2
          // println!("{}", s1); // 這裡編譯會報錯，因為 s1 已不再擁有該值
          println!("{}", s2);
      }

在 Rust 中，當 s1 的所有權轉移給 s2 時，s1 就不再有效。這樣可以避免懸垂指標或重複釋放記憶體的問題。


#### C++ 範例：

    fn main() {
          let s1 = String::from("hello");
          let s2 = s1; // s1 的所有權轉移給 s2
          // println!("{}", s1); // 這裡編譯會報錯，因為 s1 已不再擁有該值
          println!("{}", s2);
      }
  
在 C++ 中，s1 和 s2 是兩個獨立的對象，持有相同的字串內容。如果需要手動管理內存，開發者容易犯錯，導致內存洩漏或重複釋放。

#### 2. 記憶體安全與借用檢查器

Rust 使用借用檢查器在編譯時確保沒有資料競爭。 Rust 有兩種借用方式：不可變借用和可變借用。

#### Rust 範例：

     fn main() {
          let mut s = String::from("hello");
          let r1 = &s; // 不可變借用
          let r2 = &s; // 可以有多個不可變借用
          // let r3 = &mut s; // 這裡編譯會報錯，不能同時有可變和不可變借用
          println!("{} and {}", r1, r2);
      }
  
Rust 不允許同時存在一個可變借用和多個不可變借用，避免了資料競爭。而在 C/C++ 中，這種控制需要開發者自己處理，容易產生並發問題。

#### 3. 記憶體分配與釋放

在 Rust 中，記憶體管理透過其所有權系統和自動的記憶體回收機制來處理，避免了手動的 malloc/free 或 new/delete 操作。

#### Rust 範例：

    fn main() {
        let s = String::from("hello"); // String 類型自動分配記憶體
        // 當 s 超出作用域時，Rust 會自動釋放該記憶體
    }
Rust 會自動處理資源釋放，而 C/C++ 中則需要明確管理記憶體。

#### C++ 範例：

    #include <iostream>
    
    int main() {
        std::string* s = new std::string("hello");
        std::cout << *s << std::endl;
        delete s; // 必須手動釋放內存
        return 0;
    }
在 C++ 中，開發者需要手動釋放內存，稍微不慎就會導致記憶體洩漏或其他記憶體管理問題。

#### 4. 錯誤處理
Rust 沒有異常機制（不像 C++ 的 try-catch），而是使用 Result 和 Option 類型來進行明確錯誤處理。
這種錯誤處理方式讓開發者在編譯時處理所有可能的錯誤狀況，避免了隱藏錯誤。

#### Rust 範例：

    fn divide(dividend: f64, divisor: f64) -> Result<f64, String> {
        if divisor == 0.0 {
            Err(String::from("Cannot divide by zero"))
        } else {
            Ok(dividend / divisor)
        }
    }
    
    fn main() {
        match divide(10.0, 0.0) {
            Ok(result) => println!("Result: {}", result),
            Err(e) => println!("Error: {}", e),
        }
    }

Rust 強制開發者處理所有可能的錯誤，Result 和 Option 使得程式碼更加健壯。

#### C++ 範例：

    #include <iostream>
    #include <stdexcept>
    
    double divide(double dividend, double divisor) {
        if (divisor == 0) {
            throw std::invalid_argument("Cannot divide by zero");
        }
        return dividend / divisor;
    }
    
    int main() {
        try {
            std::cout << divide(10.0, 0.0) << std::endl;
        } catch (const std::invalid_argument& e) {
            std::cout << "Error: " << e.what() << std::endl;
        }
        return 0;
    }

C++ 使用異常處理，雖然靈活，但可能會導致異常不透明的問題。

#### 5. 線程與並發

Rust 提供了內建的線程安全機制，並且透過所有權系統，防止資料競爭。 
Rust 中的並發程式設計是安全的，而 C/C++ 需要開發者自行處理並發的所有問題。

#### Rust 範例：

use std::thread;

    fn main() {
        let handle = thread::spawn(|| {
            for i in 1..10 {
                println!("hi number {} from the spawned thread!", i);
            }
        });
    
        for i in 1..5 {
            println!("hi number {} from the main thread!", i);
        }
    
        handle.join().unwrap(); // 等待執行緒完成
    }

Rust 編譯器確保執行緒之間的資料存取不會造成資料競爭，且 join 用於等待執行緒完成。

#### 5. 線程與並發

Rust 提供了內建的線程安全機制，並且透過所有權系統，防止資料競爭。 
Rust 中的並發程式設計是安全的，而 C/C++ 需要開發者自行處理並發的所有問題。

Rust 範例：

    use std::thread;
    
    fn main() {
        let handle = thread::spawn(|| {
            for i in 1..10 {
                println!("hi number {} from the spawned thread!", i);
            }
        });
    
        for i in 1..5 {
            println!("hi number {} from the main thread!", i);
        }
    
        handle.join().unwrap(); // 等待執行緒完成
    }

Rust 編譯器確保執行緒之間的資料存取不會造成資料競爭，且 join 用於等待執行緒完成。

#### C++ 範例：

    #include <iostream>
    #include <thread>
    
    void print_hi(int n) {
        for (int i = 1; i <= n; ++i) {
            std::cout << "hi number " << i << " from the spawned thread!" << std::endl;
        }
    }
    
    int main() {
        std::thread t(print_hi, 10);
    
        for (int i = 1; i <= 5; ++i) {
            std::cout << "hi number " << i << " from the main thread!" << std::endl;
        }
    
        t.join(); // 等待執行緒完成
        return 0;
    }
    
C++ 的並發程式設計依賴執行緒函式庫，手動管理同步容易出現錯誤，而 Rust 自動提供了更安全的並發處理方式。

#### 總結

Rust 和 C/C++ 的主要區別在於記憶體管理、錯誤處理和並發處理的機制。 

Rust 的所有權系統和借用檢查器使其在記憶體安全方面更加出色，減少了開發者在低階記憶體管理方面的負擔。

相較之下，C/C++ 則更靈活，但更容易引發潛在的錯誤。 

Rust 提供了更嚴格的編譯時檢查，確保程式碼安全性高、並發問題少，這使其在需要高效能和高安全性的專案中特別受歡迎。


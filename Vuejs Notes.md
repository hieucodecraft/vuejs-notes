

# Vuejs Notes

## Components

### Giao tiếp giữa các component

## Hooks

### **Computed & Methods**

-  Khi gọi **computed** ta không được thêm cặp dấu `()` đằng sau, điều đó tức là `computed` cũng **không thể nhận** tham số đầu vào như `methods` .
-  Vì không nhận tham số đầu vào nên `computed` chỉ nên dùng với các dữ liệu có sẵn trong `data` của component.
-  `computed` sẽ tính toán lại mỗi khi các biến phụ thuộc trong nó thay đổi, còn `methods` sẽ được tính toán lại bất kì khi nào nó được gọi, nên nếu biết tận dụng `computed` để tính toán các dữ liệu có sẵn thì sẽ cải thiện được performance app của các bạn. Điều tuyệt vời của computed là nó sẽ được cached nên giả sử bạn có 1 computed với hàng loạt tính toán, lặp trong đó, thì nếu các các biến phụ thuộc không thay đổi thì khi sử dụng nó sẽ chỉ mất thời gian tính 1 lần, những lần sau kết quả sẽ được sử dụng lại từ lần trước.

### Watch

- Cùng với `computed` có thể xử lý việc chỉ tính toán lại khi có sự thay đổi trên dữ liệu, giúp tiết kiệm được nhiều chi phí thì Vue cung cấp cho chúng ta một cách tổng quan hơn để xử lý những dữ liệu bị thay đổi. 

- Với việc sử dụng `watch` có thể giúp chúng ta theo dõi sự thay đổi và sau đó thực hiện những tính toán phức tạp.

  ```html
  <template>
      <div class="my-component">
          <div>{{ message }}</div>
          <div><button @click="changeMessage">Click me</button></div>
      </div>
  </template>
  
  <script>
      export default {
          data() {
              return {
                  message: 'This is my first component using binding data'
              }
          },
          methods: {
              changeMessage() {
                  this.message = 'this is new message'
              }
          },
          watch: {
              message() {
                  console.log('message changed')
              }
          }
      }
  </script>
  ```

  - Ở đây khi chúng ta click vào button thì giá trị của message sẽ bị thay đổi. Chúng ta sẽ thêm `watcher` cho `message` để mỗi lần giá trị của nó thay đổi thì ta sẽ in ra cửa sổ console một thông báo nhé.

-  Vì computed cũng sử dụng giống như những property trong `data` nên chúng ta cũng có thể `watch computed`.

  ```html
  <template>
      <div class="my-component">
          <div>{{ reverseMessage }}</div>
          <div><button @click="changeMessage">Click me</button></div>
      </div>
  </template>
  
  <script>
      export default {
          data() {
              return {
                  message: ''
              }
          },
          methods: {
              changeMessage() {
                  this.message = 'this is new message'
              }
          },
          computed: {
          	reverseMessage() {
          		return this.message.split('').reverse().join('')
          	}
          },
          watch: {
              reverseMessage() {
                  console.log('Computed changed')
              }
          }
      }
  </script>
  ```

  

-  Một trường hợp khá phổ biến là khi các bạn muốn watch khi có sự thay đổi của các thuộc tính bên trong một object.

  ```html
  <template>
      <div class="my-component">
          <div>{{ message.text }}</div>
          <div><button @click="changeMessage">Click me</button></div>
      </div>
  </template>
  
  <script>
      export default {
          data() {
              return {
                  message: {
                      type: 'greeting',
                      text: 'How are you?'
                  }
              }
          },
          methods: {
              changeMessage() {
                  this.message.text = 'this is new message'
              }
          },
          watch: {
              message: {
                  handler: function () {
                      console.log('something changed')
                  },
                  deep: true
              }
          }
      }
  </script>
  ```
-   **Watch** với một thuộc tính cụ thể thì làm thế nào, chẳng hạn muốn **watch** với chỉ `type` hoặc `text` .  Chúng ta tạo ra `computed` với giá trị trả về là thuộc tính mong muốn `watch` của đối tượng. sau đó ở `watch` chúng ta áp dụng lên các `computed` đó 
  ```html
  <template>
      <div class="my-component">
          <div>Message type: {{ message.type }}</div>
          <div>Message text: {{ message.text }}</div>
          <div><button @click="changeType">Change type</button></div>
          <div><button @click="changeText">Change text</button></div>
      </div>
  </template>
  
  <script>
      export default {
          data() {
              return {
              	message: {
              		type: 'greeting',
              		text: 'How are you?'
              	}
              }
          },
          methods: {
              changeType() {
                  this.message.type = 'this is new type'
              },
              changeText() {
                  this.message.text = 'this is new text'
              }
          },
          computed: {
              getType() {
                  return this.message.type
              },
              getText() {
                  return this.message.text
              }
          },
          watch: {
              getType() {
              	console.log('type changed')
              },
              getText() {
              	console.log('text changed')
              }
          }
      }
  </script>
  ```

  

1. Test
2. Test

## Directives

### **v-for & v-if**

- Khi được dùng trên dùng một node, `v-for` có độ ưu tiên cao hơn `v-if`, có nghĩa là `v-if` sẽ được thực thi một cách riêng biệt trên mỗi vòng lặp của `v-for` .
  Ví dụ:

  ```html
  <ul>
      <li v-for="todo in todos" v-if="!todo.isComplete">
        {{ todo }}
      </li>
  </ul>
  ```
  
   **=> Điều này không tốt khi mỗi lần Vue render ra một phần tử nó lại phải kiểm tra `v-if` 1 lần. Vì for ưu tiên hơn if.**
  
  ```html
  <template>
      <div class="list-rendering">
          <ul>
              <li v-for="todo in listCompleteTodo">
              {{ todo.name }} - {{ todo.isComplete }}
              </li>
          </ul>
      </div>
  </template>

  <script>
      export default {
          data() {
              return {
                  todos: [
                      {
                          name: 'to do 1',
                          isComplete: true
                      },
                      {
                          name: 'to do 2',
                          isComplete: false
                      },
                      {
                          name: 'to do 3',
                          isComplete: true
                      },
                  ]
              }
          },
          computed: {
              listCompleteTodo() {
                  return this.todos.filter(item => item.isComplete)
              }
          }
      }
  </script>
  ```

  - **Bởi vì computed chỉ tính toán lại khi dữ liệu phụ thuộc bị thay đổi** vì thế nên ở ví dụ trên ta sẽ loại bỏ được việc kiểm tra `v-if` ở mỗi lần lặp, do đó với các tập dữ liệu mà ít khi thay đổi trong suốt vòng đời của component.
  
  ```html
  <my-component v-for="item in items" :key="item.id"></my-component>
  ```

- Chú ý giá trị của mỗi `item` lấy được từ `items` sẽ không được truyền tự động vào component mà chúng phải truyền qua `props` .  Lí do item không được truyền tự động vào component là bởi vì nếu làm vậy component sẽ bị gắn chặt vào `v-for`. Bằng cách bắt buộc khai báo nguồn dữ liệu một cách minh bạch (explicit), chúng ta có thể sử dụng lại component trong các tình huống khác. 
  
  -  **Vue sẽ bắt buộc chúng ta sử dụng "key" khi dùng v-for với component** .

### **v-if**

-  `v-if` không render ngay ra toàn bộ các element mà tùy vào giá trị kiểm tra, còn với `v-show` sẽ render ra ngay từ đầu và chỉ quyết định có `display` nó hay không.
-  Nếu nội dung trong block mà ít, và trong quá trình sử dụng mà user sẽ tác động đến block nhiều thì dùng `v-show` sẽ cho tốc độ tốt hơn, còn nếu nội dung trong block mà nhiều cùng với nhiều xử lý hoặc nội dung của block ít thay đổi trong suốt vòng đời của component thì khi đó không nên dùng `v-show` vì lúc đó trang của chúng ta sẽ nặng làm giảm performance và ta cần nghĩ đến dùng `v-if`. 
  
  **=> v-if:** mỗi lần click vào button thì thẻ `div` chứa block cũ sẽ được **thay thế** nội dung của block mới. 
  
  **=> v-show:** Khi inspect các bạn có thể thấy ngay là Vue đã render sẵn ra luôn 2 element chính là 2 block của chúng ta, và tùy với giá trị của `isActive` mà block đó có `style` là `display: none` hay không.
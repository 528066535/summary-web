### 一. 防抖

#### 问题

在项目中可能会遇到一些问题，比如滚动的时候一直会触发滚动事件，其实只需要调用一次。
所谓防抖，就是指触发事件后在 n 秒内函数只能执行一次，如果在 n 秒内又触发了事件，则会重新计算函数执行时间。

方法一（非立即执行版）:

    ```
    <div id="content" style="height:150px;line-height:150px;text-align:center; color: #fff;background-color:#ccc;font-size:80px;"></div>
    <script>
        let num = 1;
        let content = document.getElementById('content');

        function count() {
            content.innerHTML = num++;
        };

        content.onmousemove = debounce(count,1000);

        function debounce(func, wait) {
                let timeout;
                return function () {
                    let context = this;
                    let args = arguments;

                    if (timeout) clearTimeout(timeout);

                    timeout = setTimeout(() => {
                        func.apply(context, args)
                    }, wait);
                }
            }
    </script>
    ```

方法二（立即执行版）:

    ```
    function debounce(func,wait) {
        let timeout;
        return function () {
            let context = this;
            let args = arguments;

            if (timeout) clearTimeout(timeout);

            //clearTimeout调用后实际timeout也是有值的
            let callNow = !timeout;
            //重新开启定时器，指定时间内timeout不会为null
            timeout = setTimeout(() => {
                timeout = null;
            }, wait)

            if (callNow) func.apply(context, args)
        }
    }
    ```

结合使用

    ```
    function debounce(func,wait,immediate) {
        let timeout;

        return function () {
            let context = this;
            let args = arguments;

            if (timeout) clearTimeout(timeout);
            if (immediate) {
                var callNow = !timeout;
                timeout = setTimeout(() => {
                    timeout = null;
                }, wait)
                if (callNow) func.apply(context, args)
            }
            else {
                timeout = setTimeout(function(){
                    func.apply(context, args)
                }, wait);
            }
        }
    }
    ```

### 二. 节流

#### 问题

比如有些时候，搜索按钮可以无限的一直点击，一直调用接口，这样跟受到黑客攻击了一样的感觉，我们需要设置某个时间段内，请求一次就好。

方法一（时间戳版本）：

    ```
    function throttle(func, wait) {
        let previous = 0;
        return function() {
            let now = Date.now();
            let context = this;
            let args = arguments;
            if (now - previous > wait) {
                func.apply(context, args);
                previous = now;
            }
        }
    }
    ```

方法二（定时器版）：
function throttle(func, wait) {
    let timeout;
    return function() {
        let context = this;
        let args = arguments;
        if (!timeout) {
            timeout = setTimeout(() => {
                timeout = null;
                func.apply(context, args)
            }, wait)
        }

    }
}
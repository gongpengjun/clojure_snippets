# Clojure中对Map的keyword执行转string操作

--------------------------------------------------------------------

## 1 递归将map的key从keyword转为string

### 1.1 代码实现

下面实现摘自 [stringify-keys](https://clojuredocs.org/clojure.walk/stringify-keys) 源码


```clojure
(defn stringify-keys
  "Recursively transforms all map keys from keywords to strings."
  {:added "1.1"}
  [m]
  (let [f (fn [[k v]] (if (keyword? k) [(name k) v] [k v]))]
    ;; only apply to maps
    (postwalk (fn [x] (if (map? x) (into {} (map f x)) x)) m)))
```

### 1.2 使用示例


```shell
user=> (use 'clojure.walk)
user=> (stringify-keys {:a :hello :b :world})
{"a" :hello, "b" :world}
user=> (stringify-keys {:a {:hello :world} :b [{:love :clojure} {:hate :other}]})
{"a" {"hello" :world}, "b" [{"love" :clojure} {"hate" :other}]}
```

--------------------------------------------------------------------

## 2 递归将map的value从keyword转为string

### 2.1 代码实现

```clojure
(defn stringify-vals
  "Recursively transforms all map vals from keywords to strings."
  [m]
  (let [f (fn [[k v]] (if (keyword? v) [k (name v)] [k v]))]
    ;; only apply to maps
    (postwalk (fn [x] (if (map? x) (into {} (map f x)) x)) m)))
```

### 2.2 使用示例

```shell
user=> (use 'clojure.walk)
user=> (defn stringify-vals
  #_=>   "Recursively transforms all map vals from keywords to strings."
  #_=>   [m]
  #_=>   (let [f (fn [[k v]] (if (keyword? v) [k (name v)] [k v]))]
  #_=>     ;; only apply to maps
  #_=>     (postwalk (fn [x] (if (map? x) (into {} (map f x)) x)) m)))
#'user/stringify-vals
user=> (stringify-vals {:a :hello :b :world})
{:a "hello", :b "world"}
user=> (stringify-vals {:a {:hello :world} :b [{:love :clojure} {:hate :other}]})
{:a {:hello "world"}, :b [{:love "clojure"} {:hate "other"}]}
```

--------------------------------------------------------------------

## 3 递归将map的key和value从keyword转为string

### 3.1 代码实现

Recursively transforms all map keys and values from strings to keywords.

```clojure
(defn stringify-keys-and-vals
  "Recursively transforms all map keys and vals from keywords to strings."
  [m]
  (let [kf (fn [[k v]] (if (keyword? k) [(name k) v] [k v]))
  		vf (fn [[k v]] (if (keyword? v) [k (name v)] [k v]))]
    ;; only apply to maps
    (postwalk (fn [x] (if (map? x) (into {} (map #(-> % kf vf) x)) x)) m)))
```

### 3.2 使用示例

```shell
user=> (use 'clojure.walk)
user=> (defn stringify-keys-and-vals
  #_=>   "Recursively transforms all map keys and vals from keywords to strings."
  #_=>   [m]
  #_=>   (let [kf (fn [[k v]] (if (keyword? k) [(name k) v] [k v]))
  #_=>   vf (fn [[k v]] (if (keyword? v) [k (name v)] [k v]))]
  #_=>     ;; only apply to maps
  #_=>     (postwalk (fn [x] (if (map? x) (into {} (map #(-> % kf vf) x)) x)) m)))
#'user/stringify-keys-and-vals
user=> (stringify-keys-and-vals {:a :hello :b :world})
{"a" "hello", "b" "world"}
user=> (stringify-keys-and-vals {:a {:hello :world} :b [{:love :clojure} {:hate :other}]})
{"a" {"hello" "world"}, "b" [{"love" "clojure"} {"hate" "other"}]}
```

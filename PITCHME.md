# Списъци и речници

#HSLIDE

## Списъци

![Image-Absolute](assets/list.jpg)

#HSLIDE

```elixir
[1, :two, "three", %{four: 4}, 'five', [6, [:seven], "eight"]]
```

#HSLIDE

#### [head | tail]

```
+-|-+  +-|-+  +-|-+  +-|-+  +-|-+
|1| -->|2| -->|3| -->|4| -->|5| |
+-|-+  +-|-+  +-|-+  +-|-+  +-|-+
```

#HSLIDE

#### Конструиране

```elixir
a = [1, 2, 3]    # [1, 2, 3]
b = [0 | a]      # [0, 1, 2, 3]
c = [-2, -1 | b] # [-2, -1, 0, 1, 2, 3]
d = [a | b]      # [[1, 2, 3], 0, 1, 2, 3]
e = a ++ b      # [1, 2, 3, 0, 1, 2, 3]
```

#HSLIDE

#### Pattern Matching

```elixir
[a | b] = [1,2,3]
a  # 1
b  # [2, 3]
[x, y | z] = [4, 5]
x  # 4
y  # 5
z  # []
```

#HSLIDE

#### Pattern Matching

```elixir
[[{x, _} | a] | b] = [[{9, 10} , 2, 3], 0]
x  # 9
a  # [2, 3]
b  # [0]
```

#HSLIDE

### Mapreduce all the way down

![Image-Absolute](assets/mapreduce.jpg)

#HSLIDE

#### Обхождане (Рекурси)

```elixir
defmodule ListUtils do
  def length([]), do: 0
  def length([_head | tail]), do: 1 + length(tail)
end

ListUtils.length(["cat", "dog", "fish", "horse"]) # 4
```

#HSLIDE

```
ListUtils.length(["cat", "dog", "fish", "horse"])
= 1 + ListUtils.length(["dog", "fish", "horse"])
= 1 + 1 + ListUtils.length(["fish", "horse"])
= 1 + 1 + 1 + ListUtils.length(["horse"])
= 1 + 1 + 1 + 1 + ListUtils.length([])
= 1 + 1 + 1 + 1 + 0
= 4
```

#HSLIDE

#### Обхождане (Опашкова рекурси)

```elixir
defmodule ListUtils do
  def length(list) when is_list(list), do: do_length(list)

  defp do_length(list, n \\ 0)
  defp do_length([], n), do: n
  defp do_length([head | tail], n), do: do_length(tail, n + 1)
end
```

#HSLIDE

#### Reduce

```elixir
defmodule ListUtils do
  def reduce(list, func, acc \\ 0)
  when is_list(list) and is_function(func) do
    do_reduce(list, func, acc)
  end

  def do_reduce([], _func, acc), do: acc
  def do_reduce([head | tail], acc, func) do
    reduce(tail, func.(head, acc), func)
  end
end

list_length = &ListUtils.reduce(&1, fn _head, acc -> 1 + acc end, 0)

list_length.(["cat", "dog", "horse"]) # 3
```


#HSLIDE

#### Изграждане

```elixir
defmodule ListUtils do
  def filter(list, predicate)
  when is_list(list) and is_function(predicate) do
    do_filter(list, predicate)
  end

  defp do_filter(list, predicate, result \\ [])
  defp do_filter([], _,predicate result), do: result
  defp do_filter([head | tail], predicate, result) do
    case predicate.(head) do
      true -> do_filter(tail, predicate, result ++ [head])
      false -> do_filter(tail, predicate, result)
    end
  end
end

ListUtils.filter([1,2,3,4,5], &(rem(&1, 2) == 0)) # => [2, 4]
```

#HSLIDE

```elixir
a = [1,2,3]
b = a ++ [4]
```

vs.

```elixir
a = [1,2,3]
b = [0 | a]
```

#HSLIDE

#### Изграждане

```elixir
defmodule ListUtils do
  def reverse(list) when is_list(list) do
    do_revers(list)
  end

  defp do_reverse(list, result \\ [])
  defp do_reverse([], resilt), do: result
  defp do_reverse([head | tail], result) do
    do_result(tail, [head | result])
  end
end

ListUtils.reverse([1,2,3,4,5]) # [5, 4, 3, 2, 1]
```


#HSLIDE

#### Изграждане

```elixir
defmodule ListUtils do
  def filter(list, predicate)
  when is_list(list) and is_function(predicate) do
    do_filter(list, predicate)
  end

  defp do_filter(list, predicate, result \\ [])
  defp do_filter([], _predicate, result), do: reverse(result)
  defp do_filter([head | tail], predicate, result) do
    case predicate.(head) do
      true -> do_filter(tail, predicate, [head | result])
      false -> do_filter(tail, predicate, result)
    end
  end
end

ListUtils.filter([1,2,3,4,5], &(rem(&1, 2) == 0)) # [2, 4]
```

#HSLIDE

#### Map

```elixir
defmodule ListUtils do
  def map(list, func)
  when is_list(list) and is_function(func) do
    do_map(list, func)
  end

  def do_map(list, func, result \\ [])
  def do_map([], _func, result), do: reverse(result)
  def do_map([head | tail], func, result) do
    do_map(tail, func, [func.(head) | result])
  end
end

ListUtils.map([1,2,3,4,5], &(&1 * &1)) [1,4,9,16,25]
```

#HSLIDE

#### Няколко съвета

- отворете и разгледайте документацията на `List` модула
- внимавайте при конструирането на списъци
- научете какво е опашкова рекурсия
- ако обръщате списък, използвайте `Enum.reverse/1` или `:list.reverse/1`

#HSLIDE

```elixir
defmodule Enum
  @spec reverse(t) :: list
  def reverse(enumerable)

  def reverse([]), do: []
  def reverse([_] = list), do: list
  def reverse([item1, item2]) do
    [item2, item1]
  end
  def reverse([item1, item2 | rest]) do
    :lists.reverse(rest, [item2, item1])
  end
  def reverse(enumerable) do
    reduce(enumerable, [], &[&1 | &2])
  end
end
```

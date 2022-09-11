Concatenating Subarrays
=======================

Feb 22, 2021 · 3 min read

Дана двумерный и обычный массивы чисел. Можно ли все отдельные массивы из двумерного найти в обычном массиве, так, чтобы _не было пересечений_?

Пример:

    groups = [[1,-1,-1],[3,-2,0]]
    nums = [1,-1,0,1,-1,-1,3,-2,0]
    

![](/images/concatenating-arrays--ex.jpg)

В данном случае, мы можем «намапить» группы на исходный массив — надо вернуть true.

Другой пример:

    groups = [[1,2,3],[3,4]]
    nums = [7,7,1,2,3,4,7,7]
    

**1,2,3**,4 и 1,2,**3,4** хоть и совпадают с группами, но не без пересечений. Надо вернуть false.

Порядок важен — его надо сохранить, то есть при поиске местами группы переставлять нельзя.

[Задача на LeetCode](https://leetcode.com/problems/form-array-by-concatenating-subarrays-of-another-array/).

Решение [#](#решение)
---------------------

Это задача хороший пример где удобно использовать рекурсию. Для рекурсивного решения необходимы _базовый случай_ и _рекуррентное соотношение_.

Базовый случай — нет групп. Если нечего искать в исходном массиве, значит считаем, что «всё нашлось».

Рекуррентное соотношение — если мы нашли одну группу в массиве, исходный результат равен результату для _хвоста массива_ и групп за вычетом найденной.

Мы ищем решения без пересечений, поэтому как только одна группа нашлась, можно смело отрезать весь массив до конца группы и запустить рекурсивный поиск для хвоста.

![](/images/concatenating-arrays--ex2.jpg)

Сложность данного решения `O(N * M)`, где `N` количество исходных элементов, а `M` — количество элементов в группах.

Рассмотрим «худший случай»: `[1,1,1,1,1,1,2,1,1,1,1,1,1,2]`, `[[1,1,2],[1,1,2]]`. Из-за совпадающих префиксов мы будем для каждой единички пробегаться по всей группе заново, останавливаясь только на последнем элементе.

Это похоже на поиск подстроки в строке, где сложность квадратная по аналогичной причине.

    /**
     * @param {number[][]} groups
     * @param {number[]} nums
     * @return {boolean}
     */
    var canChoose = function(groups, nums) {
      // функция для рекурсивного
      // поиска подмассивов,
      // принимаем начало для поиска
      // в исходном массиве (начало хвоста),
      // и индекс текущей группы.
      // Удобно оперировать индексами
      // а не менять исходные массивы,
      // чтобы сэкономить время
      // на лишних аллокациях / деаллокациях
      function next(start, groupIndex) {
        // базовый случай —
        // поискали по всем группам
        if (groups.length === groupIndex) {
          return true;
        }
        // начинаем поиск в хвосте
        for (let i = start; i < nums.length; i++) {
          // если текущая группа
          // нашлась как подмассив
          if (isSubarray(i, groupIndex)) {
            // запускаем рекурсивный поиск
            // для нового хвоста, с конца
            // найденного подмассива,
            // и следующей группы
            return next(i + groups[groupIndex].length, groupIndex + 1);
          }
        }
        // если до базового случая
        // не добрались — значит что-то пошло не так,
        // например раньше закончился массив,
        // или очередная группа нигде
        // не нашлась как подмассив,
        // в любом случае это false,
        // и в этом удобство рекурсивного решения здесь
        return false;
      }
    
      // хелпер для поиска подмассива,
      // просто проверяет
      // с указанного места массива
      // совпадёт ли он с указанной группой
      function isSubarray(start, groupIndex) {
        for (let i = 0; i < groups[groupIndex].length; i++) {
          if (nums[start + i] !== groups[groupIndex][i]) {
            return false;
          }
        }
        return true;
      }
      // запускаем рекурсия с начала
      // исходного массива и первой группы
      return next(0, 0);
    };
    

![](/images/concatenating-arrays--result.jpg)

PS. Обсудить можно в [телеграм-чате](https://t.me/ctci_chat_ru) любознательных программистов. Welcome! 🤗

Подписывайтесь на [мой твитер](https://twitter.com/vitkarpov) или [канал в телеграме](https://t.me/coding_interviews), чтобы узнавать о новых разборах задач.
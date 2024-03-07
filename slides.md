---
theme: seriph
background: bg.jpg
class: text-center
highlighter: shiki
lineNumbers: false
drawings:
  persist: false
title: Asynchroniczny JavaScript
mdc: true
monaco: true
monacoTypesSource: local
---

# Asynchroniczny JavaScript

Czyli w jaki sposób kobiety radzą sobie z wielozadaniowością.


<div class="abs-br m-6 flex gap-2">
  <a href="https://github.com/wvffle/slides-wasm" target="_blank" alt="GitHub"
    class="text-xl icon-btn opacity-50 !border-none !hover:text-white flex items-center">
    <carbon-logo-github class="mr-4 -mb-0.5" />
    Kasper Seweryn
  </a>
</div>

---

# Czym jest kod asynchroniczny?
Kod asynchroniczny to taki, który nie jest wykonywany w sposób sekwencyjny. Następne linie kodu mogą być wykonywane zanim poprzednie się zakończą.

<v-clicks>
  <img src="https://i.imgur.com/Zo4tmNe.png" />
  <img src="https://i.imgur.com/6G339If.png" />
</v-clicks>

---

# Współbieżność i równoległość
Jako dwa rodzaje asynchroniczności

<v-clicks>

- **Współbieżność** *(ang: concurrency)* - Możliwość rozpoczęcia wielu zadań i przełączanie między nimi w celu ich wykonania.
- **Równoległość** *(ang: parallelism)* - Wykonywanie wielu zadań jednocześnie, w tym samym czasie.

<div>
<center style="line-height: 1 !important">

<pre>
  <code>
         Concurrency          Concurrency + parallelism
  (Single-Core CPU)           (Multi-Core CPU)
  ┌───┐                       ┌───┬───┐
  │th1│                       │th1│th2│
  │   │                       │   ├───┘
  └───┼───┐                   │   ├───┐
      │th2│                   └───┤th2│
  ┌───┼───┘                   ┌───┼───┘
  │th1│                       │th1│    
  └───┼───┐                   │   ├───┐
      │th2│                   │   │th2│

  </code>
</pre>


</center>
Źródło: https://stackoverflow.com/a/30761061
</div>
</v-clicks>

---

# JavaScript - asynchroniczność
JavaScript jest jednowątkowy, co oznacza, że może wykonywać tylko jedno zadanie naraz. 

A jednak istnieją mechanizmy, które pozwalają na asynchroniczne wykonywanie kodu.

```javascript
async function fetchUser(id) {
  const user = await fetch(`/users/${id}`)
  return user
}

const user1 = fetchUser(1)
const user2 = fetchUser(2)

console.log(await user1, await user2)
```

---

# Event Loop
Podstawowe pojęcia

<v-clicks>

- **Call Stack** - stos wywołań, śledzi wywołania funkcji. Jest to stos LIFO składający się z ramek. Każda ramka reprezentuje wywołanie funkcji.
- **Task Queue** - kolejka zadań to kolejka FIFO zadań, które zostaną wykonane przez pętlę zdarzeń w przyszłości. Zadania są synchronicznymi blokami kodu, które mogą umieszczać inne zadania w kolejce, podczas gdy są wykonywane.
- **Microtask Queue** - kolejka mikrozadań. Kolejka FIFO mikrozadań, które wykonywane są przed tym jak kolejne zadanie zostanie rozpoczęte.
- **Event Loop** - pętla zdarzeń przetwarza zadania i mikrozadania. Umieszcza je na stosie wywołań w celu wykonania pojedynczo.

</v-clicks>


<!-- 
żeby to zrozumieć, trzeba wpierw zrozumieć event loop



-->

---

# Event Loop
Zasada działania

<v-clicks>

- Pętla zdarzeń działa poprzez ciągłe przeglądanie kolejki zadań i przetwarzanie zawartych w niej zadań jeden po drugim. 
- Aby przetworzyć zadanie, pętla zdarzeń wywołuje związaną z nim funkcję. Podczas gdy zadanie jest wykonywane, ma ono wyłączny dostęp do stosu wywołań. Pętla zdarzeń czeka na przetworzenie kolejnego zadania, dopóki obecne zadanie nie zostanie zakończone, a stos wywołań będzie pusty.
- Podczas gdy zadanie jest wykonywane, może ono umieszczać inne zadania w kolejce do przetworzenia w kolejnych iteracjach pętli zdarzeń.
- Zadania mogą także pochodzić z zewnętrznych źródeł, takich jak zdarzenia DOM i zdarzenia sieciowe.

</v-clicks>

---
layout: center
---

# Przykład działania

- [Przykład 1](https://www.jsv9000.app/?code=ZnVuY3Rpb24gdGVudGgoKSB7IH0KCmZ1bmN0aW9uIG5pbnRoKCkgeyB0ZW50aCgpIH0KCmZ1bmN0aW9uIGVpZ3RoKCkgeyBuaW50aCgpIH0KCmZ1bmN0aW9uIHNldmVudGgoKSB7IGVpZ3RoKCkgfQoKZnVuY3Rpb24gc2l4dGgoKSB7IHNldmVudGgoKSB9CgpmdW5jdGlvbiBmaWZ0aCgpIHsgc2l4dGgoKSB9CgpmdW5jdGlvbiBmb3VydGgoKSB7IGZpZnRoKCkgfQoKZnVuY3Rpb24gdGhpcmQoKSB7IGZvdXJ0aCgpIH0KCmZ1bmN0aW9uIHNlY29uZCgpIHsgdGhpcmQoKSB9CgpmdW5jdGlvbiBmaXJzdCgpIHsgc2Vjb25kKCkgfQoKZmlyc3QoKTs%3D)
- [Przykład 2](https://www.jsv9000.app/?code=ZnVuY3Rpb24gbG9nQSgpIHsgY29uc29sZS5sb2coJ0EnKSB9CmZ1bmN0aW9uIGxvZ0IoKSB7IGNvbnNvbGUubG9nKCdCJykgfQpmdW5jdGlvbiBsb2dDKCkgeyBjb25zb2xlLmxvZygnQycpIH0KZnVuY3Rpb24gbG9nRCgpIHsgY29uc29sZS5sb2coJ0QnKSB9CgovLyBDbGljayB0aGUgIlJVTiIgYnV0dG9uIHRvIGxlYXJuIGhvdyB0aGlzIHdvcmtzIQpsb2dBKCk7CnNldFRpbWVvdXQobG9nQiwgMCk7ClByb21pc2UucmVzb2x2ZSgpLnRoZW4obG9nQyk7CmxvZ0QoKTsKCi8vIE5PVEU6Ci8vICAgVGhpcyBpcyBhbiBpbnRlcmFjdGl2ZSB2aXp1YWxpemF0aW9uLiBTbyB0cnkgCi8vICAgZWRpdGluZyB0aGlzIGNvZGUgYW5kIHNlZSB3aGF0IGhhcHBlbnMuIFlvdQovLyAgIGNhbiBhbHNvIHRyeSBwbGF5aW5nIHdpdGggc29tZSBvZiB0aGUgZXhhbXBsZXMgCi8vICAgZnJvbSB0aGUgZHJvcGRvd24h)
- [Przykład 3](https://www.jsv9000.app/?code=ZnVuY3Rpb24gZmV0Y2hKb2tlKCkgewogIHJldHVybiBQcm9taXNlLnJlc29sdmUoKS50aGVuKCgpID0%2BIAogICAgZmV0Y2goYGh0dHBzOi8vYXBpLmNodWNrbm9ycmlzLmlvL2pva2VzL3JhbmRvbWApCiAgICAgIC50aGVuKChkYXRhKSA9PiBkYXRhLmpzb24oKSkKICAgICAgLnRoZW4oKGpva2UpID0%2BIGpva2UudmFsdWUpCiAgKQp9Cgpjb25zdCBqb2tlMSA9IGZldGNoSm9rZSgpCmNvbnN0IGpva2UyID0gZmV0Y2hKb2tlKCkKCgpQcm9taXNlLmFsbChbCiAgam9rZTEsCiAgam9rZTIKXSkudGhlbihqb2tlcyA9PiBqb2tlcy5mb3JFYWNoKGpva2UgPT4gY29uc29sZS5sb2coam9rZSkpKQ%3D%3D)

---

# JavaScript - równoległość
Ale przecież JavaScript jest jednowątkowy!?

<v-clicks>

To prawda, ale równoległość w JavaScript jest możliwa dzięki Web Workerom, które nie są są częścią silnika JavaScript, ale są dostępne w API przeglądarki.

Każdy Web Worker działa w osobnym wątku, co pozwala na równoległe wykonywanie skryptów, nie blokując przy tym interfejsu użytkownika.

</v-clicks>

---

# Web Worker - przykład

```javascript
// main.js
const logFactorial = function(number) {
  const worker = new Worker('factorial.js');

  worker.onmessage = function(event) {
    console.log(event.data);
  };

  worker.postMessage(number);
};

logFactorial(5);
```

```javascript
// factorial.js
self.onmessage = function(event) {
  const number = event.data;
  
  let result = 1;
  for (let i = 1; i <= number; i++) {
    result *= i;
  }
  
  self.postMessage(result);
};
```

---

# Główny wątek, Web Worker i Event Loop

https://stackblitz.com/edit/stackblitz-starters-x7jnmh?file=script.js,fib.js

---


# Ludzki mózg - współbieżny czy równoległy?

<div>

<v-click>
Nasze mózgi działają równolegle i są w stanie wykonywać wiele zadań i przetwarzać wiele rodzajów informacji jednocześnie.
</v-click>

<v-clicks>

- *Działania nieświadome:* Oddychanie, bicie serca, rozszerzanie źrenic, trawienie.
- *Działania świadome:* Prowadzenie auta, rozmowa telefoniczna, słuchanie radia, czytanie wiadomości.

</v-clicks>
<v-clicks>

Możemy skupić uwagę tylko na jednej rzeczy naraz.

Co sprawia, że jesteśmy bardziej współbieżni niż równolegli.

</v-clicks>
</div>

<!--
Skąd bierze się powiedzenie, że kobiety mogą robić wiele rzeczy na raz? Co to znaczy? Przeanalizujmy to.
-->
---
layout: center
---

<center>
Dlatego przepraszam wszystkie kobiety,
<div><v-click>
które myślały że są wielozadaniowe.
</v-click></div>
<div><v-click>
Jesteście współbieżne.
</v-click></div>
<div><v-click>
Jak JavaScript.
</v-click></div>
</center>

---
layout: center
---

# Dziękuję za uwagę

---
layout: center
---

<img src="https://i.imgur.com/7cWWXF6.png" style="height: 400px" />

<!-- 

Mikrozadania różnią się od zadań tym, że są przechowywane w innym miejscu oraz są wywoływane w innym czasie (wszytkie po zakończeniu zadania)

Mikrozadania są po to by ogarniać Promisy

-->

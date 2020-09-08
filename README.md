# Veien Til Vakker Scheme

_Velmenende råd fra gruppelærerne i IN2040, basert på studentenes innleveringer._

1.  [Scheme er ikke Java I](#rule-0)
2.  [Scheme er ikke Java II](#rule-1)
3.  [`eq?` vs `=` vs `equal?`](#rule-2)
4.  [`if` vs `cond`](#rule-3)
5.  [Alt har en sannhetsverdi](#rule-4)
6.  [Prosedyrer av høyere orden](#rule-5)
7.  [Kjenn kjernespråket](#rule-6)
8.  [Dokumentasjon](#rule-7)
9.  [Navn med mening](#rule-8)

## Scheme er ikke Java I [[↑]](#top)<a name="rule-0"></a>

I Java lukker man gjerne krøllparenteser på egne linjer. Det er en vane man bør legge av seg når man skriver Scheme, slik at koden ikke blir seende slik ut:

    (define (factorial n)
      (if (zero? n)
          1
          (* n (factorial (- n 1)))
          )
      )

Men heller slik:

    (define (factorial n)
      (if (zero? n)
          1
          (* n (factorial (- n 1)))))

## Scheme er ikke Java II [[↑]](#top) <a name="rule-1"></a>

I Java gir man gjerne variabler navnSomSerSlikUt:

    fastExpt
    growHuffmanTree
    callWithCurrentContinuation

I Scheme er det konvensjon å navngi variabler på-en-mer-lesbar-måte:

    fast-expt
    grow-huffman-tree
    call-with-current-continuation

## `eq?` vs `=` vs `equal?` [[↑]](#top) <a name="rule-2"></a>

Scheme definerer en hærskare av ulike ekvivalenspredikater. For vårt bruk er det viktigst å merke seg forskjellen på `eq?`, `=` og `equal?` (Den fulle historien finnes i [R5RS](http://www.schemers.org/Documents/Standards/R5RS/HTML/r5rs-Z-H-9.html#%_sec_6.1)). Gode tommelfingerregler kan være å bruke …

*   … `eq?` for å sjekke om to uttrykk refererer til det samme **objektet** (bruk for eksempel for **symboler** og **cons-celler**).
*   … `=` for å sammenligne **tall**.
*   … `equal?` **ellers**.

Vær spesielt oppmerksom på at noen tilfeller har uspesifisert oppførsel, selv om ikke Racket nødvendigvis gir noen feilmelding:

    (= 'a 'a)               ; ==> typefeil
    (= 'a 'b)               ; ==> typefeil
    (eq? 1 1)               ; ==> udefinert
    (eq? '(x y z) '(x y z)) ; ==> udefinert
    (eq? "hest" "hest")     ; ==> udefinert

Det er derfor viktig at du vet når du bør bruke de ulike ekvivalenspredikatene:

    (eq? 'a 'a)                ; ==> #t
    (eq? 'a 'b)                ; ==> #f
    (= 1 1)                    ; ==> #t
    (equal? '(x y z) '(x y z)) ; ==> #t
    (equal? "hest" "hest")     ; ==> #t

## `if` vs `cond` [[↑]](#top) <a name="rule-3"></a>

Lange `if`-forgreninger er tunge å lese:

    (define (sign n)
      (if (< 0 n)
          "positive"
          (if (> 0 n)
              "negative"
              "zero")))

Bruk heller `if` når det bare er to mulige utfall, og `cond` når det er flere:

    (define (sign n)
      (cond ((< 0 n) "positive")
            ((> 0 n) "negative")
            (else "zero")))

## Alt har en sannhetsverdi [[↑]](#top) <a name="rule-4"></a>

Det er fort gjort å glemme dette, og pakke uttrykk inn i overflødige `if`-blokker:

    (define (bit? n)
      (if (= n 0)
          #t
          (if (= n 1)
              #t
              #f)))

Dra heller nytte av at uttrykkene allerede har en sannhetsverdi:

    (define (bit? n)
      (or (= n 0) (= n 1)))

## Prosedyrer av høyere orden [[↑]](#top) <a name="rule-5"></a>

Høyereordens prosedyrer vil spare deg for mye dobbeltarbeid, dersom du vender deg til å gjenkjenne de vanligste mønstrene. `map`, `filter`, `accumulate` og `apply` er alle vel verdt å gjøre seg komfortabel med.

Noen mønstre dukker opp gang på gang:

    (define (list-odd lst)
      (if (null? lst)
          '()
          (cons (odd? (car lst)) (list-odd (cdr lst)))))

    (list-odd '(1 2 3 4 5)) ; ==> (#t #f #t #f #t)

Koden blir klarere dersom man greier å gjenkjenne disse:

    (map odd? '(1 2 3 4 5)) ; ==> (#t #f #t #f #t)

## Kjenn kjernespråket [[↑]](#top) <a name="rule-6"></a>

Det er unødvendig arbeid å implementere funksjonalitet som allerede finnes innebygget i språket:

    (define (bit-length bits)
      (if (null? bits)
          0
          (+ 1 (bit-length (cdr bits)))))

    (bit-length '(1 0 1 0 1)) ; ==> 5

R5RS Scheme er et [minimalistisk språk](http://www.schemers.org/Documents/Standards/R5RS/), og det kan være lønnsomt å gjøre seg kjent med standardprosedyrene:

    (length '(1 0 1 0 1)) ; ==> 5

## Dokumentasjon [[↑]](#top) <a name="rule-7"></a>

Fortell hva koden din gjør. Beskriv hva prosedyrene du skriver forventer som argumenter, og hva de returnerer. Skriv [selvforklarende kode](#rule-8), og utbroder med kommentarer når det trengs.

Legg ved kjøreeksempler. Det forbedrer forståelsen av hvordan prosedyren oppfører seg, og kan hjelpe deg med å avdekke feil.

    ;; Return `#t' if symbol `elm' is an element of `lst', else return `#f'.
    (define (member? elm lst)
      (cond ((null? lst) #f)
            ((eq? elm (car lst)) #t)
            (else (member? elm (cdr lst)))))

    ;; (member? 'a '())        ==> #f
    ;; (member? 'b '(a b c))   ==> #t
    ;; (member? 'b '(a (b) c)) ==> #f

_Emacs-tips:_ Bruk `M-;` for å kommentere ut hele blokker av gangen.

## Navn med mening [[↑]](#top) <a name="rule-8"></a>

Slapp navngiving resulterer i mysteriefull kode:

    (define (f x y)
      (define (g a b)
        (cond ((null? a) (reverse b))
              ((x (car a))
               (g (cdr a) (cons (car a) b)))
              (else (g (cdr a) b))))
      (g y '()))

Ved å gi gjennomtenkte navn på variabler og prosedyrer, blir koden selvforklarende:

    (define (filter pred? lst)
      (define (iter lst result)
        (cond ((null? lst) (reverse result))
              ((pred? (car lst))
               (iter (cdr lst) (cons (car lst) result)))
              (else (iter (cdr lst) result))))
      (iter lst '()))

_Emacs-tips:_ Bruk `M-/` for å slippe å skrive lange navn gang på gang.

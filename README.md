# Arka Code Quality Gate

## Table of Contents
1. [Premesse](#premesse)
1. [Code Quality Metrics](#code-quality-metrics)
    1. [Cyclomatic Complexity](#cyclomatic-complexity)
    1. [Method Length](#method-length)
    1. [Class Length](#class-length)
1. [Code Quality Guidelines](#code-quality-guidelines)
1. [Hexagonal Architecture](#hexagonal-architecture)
1. [Testing](#testing)
1. [Referenti Arka](#referenti-arka)
1. [Fonti](#fonti)

## Bozze

- Nomi test uguali a nomi classi
- Porte divise per concetti e non per "adapter"
  - Esempio | **BAD** una porta unica per adapter di tutti i metodi della stessa fonte | **GOOD** una porta per concetto A, una porta per concetto B, ecc.. |)
- Massimo 5 dipendenze
- Quando lo UseCase diventa troppo grande lo possiamo spezzare in più classi. Il nome di tali classi avrà il suffisso “…Auxiliary”. Queste classi ausiliarie hanno lo stesso ruolo degli use case (quindi compongono varie porte), servono solo a spezzare il codice dello use case



## Premesse

- È possibile sottomettere codice che non rispetti le [Code Quality Metrics](#code-quality-metrics) riportate ma dev'essere condiviso in code review con un [referente Arka sulla qualità](#referenti-arka), tramite PR o code review sincrona.
- In caso non venissero rispettate, le metriche e convenzioni di coding riportate nel presente documento possono essere utilizzate per rigettare o fare revert del codice pubblicato.
- In futuro potrebbe venire integrato qualche strumento di static code analysis come [SonarQube](https://www.sonarqube.org/).



## Code Quality Metrics

Il paragrafo contiene linee guida per scrivere _clean code_ secondo Arka.

Si differenzia dal paragrafo [Code Quality Guidelines](#code-quality-guidelines) in quanto contiene solo metriche misurabili (e.g. la lunghezza di un metodo).

### Overview

| Name                  | Unità di misura         | Valore massimo accettato   | Valore ottimo |
|-----------------------|-------------------------|----------------------------|---------------|
| [Cyclomatic Complexity](#cyclomatic-complexity) | Livelli di indentazione | 1        | |
| [Method Length](#method-length)                 | Righe                   | 20       | 5 |
| [Class Length](#class-length)                   | Righe                   | 100      | 50 |



### Cyclomatic Complexity

Nel tentativo di ridurre la complessità del codice (complessità ciclomatica), non superare il livello 1 di indentazione per metodo.

```java
// BAD
void stuff() {
  ... // Livello 0, OK
  if (condition) {
    ... // Livello 1, OK
    if (otherCondition) {
      ... // Livello 2, limite superato! Refactor necessario
    } else {
      ...
    }
  }
}

// GOOD
void stuff() {
  ... // Livello 0, OK
  if (condition) {
    ... // Livello 1, OK
    someOtherStuff();
  }
}

void someOtherStuff() {
  if (otherCondition) {
    ... // Livello 1, OK
  } else {
    ...
  }
}
```

#### Trattamento

Dei refactor possibili per ridurre la complessità ciclomatica potrebbero essere:
- [Extract Method](https://refactoring.guru/extract-method) `Ctrl+Alt+M`: Estrarre gli if/else innestati in metodi.
- [Extract Class](https://refactoring.guru/extract-class) `Refactor manuale`: Estrarre in classi frammenti di logica.

**[⬆ back to top](#table-of-contents)**




### Method Length

Per cercare di rispettare il [Single Responsibility Principle (SRP)](https://en.wikipedia.org/wiki/Single-responsibility_principle), viene fissato un limite di lunghezza dei metodi.

La lunghezza massima di un metodo è 20 righe. Il preferibile è 5.

#### Trattamento

Refactor possibili per ridurre la lunghezza dei metodi:
- [Extract Method](https://refactoring.guru/extract-method) `Ctrl+Alt+M`

**[⬆ back to top](#table-of-contents)**




### Class Length

Per [SRP](https://en.wikipedia.org/wiki/Single-responsibility_principle), viene fissato un limite di lunghezza delle classi.

La lunghezza massima di una classe è 100 righe. Il preferibile è entro i 50.

#### Trattamento

Refactor possibili per ridurre la lunghezza delle classi:
- [Extract Class](https://refactoring.guru/extract-class) `Refactor manuale`: helps if part of the behavior of the large class can be spun off into a separate component.
- [Extract Interface](https://refactoring.guru/extract-interface) `Refactor manuale`: helps if it's necessary to have a list of the operations and behaviors that the client can use.

> If a large class is responsible for the graphical interface, you may try to move some of its data and behavior to a separate domain object. In doing so, it may be necessary to store copies of some data in two places and keep the data consistent. Duplicate Observed Data offers a way to do this. ([Fonte](https://refactoring.guru/smells/large-class))

**[⬆ back to top](#table-of-contents)**



## Code Quality Guidelines

Il paragrafo contiene linee guida per scrivere _clean code_ secondo Arka.

Si differenzia dal paragrafo [Code Quality Metrics](#code-quality-metrics) in quanto non contiene linee guida misurabili (e.g. il nome di una variabile non è misurabile).



## Hexagonal Architecture
> TODO


## Testing

Scrivere test chiari è un complicato, forse più che scrivere *clean code* nel codice applicativo.

### Test unitari

Per scrivere un test unitario chiaro, vanno rispettate alcune regole:

Le sezioni _Arrange_, _Act_ e _Assert_ devono essere separate da una riga vuota
- **Arrange**: Sezione in cui si inizializzano gli input dei test ed eventuali mock. Input e mock vanno separati da una riga bianca
- **Act**: Sezione in cui viene chiamato il metodo che si sta testando
- **Assert**: sezione in cui si effettuano le assert

```java
@Test
void calculate_percentuale_fondo() {
    Input input = new Input(context, aliquotaFinanziaria);
    Table table = Table.builder()
             .fondo("GZ")
             .codice("COD")
             .build();
 
    when(tableRepository.findBy(aKey())).thenReturn(Cristallizzazione.from(table));
 
    Result result = service.calculate(input);
 
    assertThat(result.getFondo()).isEqualTo(new Fondo("COD", Fondo.Percentuale.max(), Fondo.Tipo.GESTIONE));
    assertThat(result.getContext()).isEqualTo(context);
}
```

**[⬆ back to top](#table-of-contents)**

### Preferire AssertJ a JUnit o Hamcrest per le asserzioni

```java
// BAD
import static org.junit.assertThat;
 
assertThat(result, Matchers.is("value"));
assertEquals("value", result);

// GOOD
import static org.assertj.core.api.Assertions.assertThat;
 
assertThat(result).isEqualTo("value");
```

**[⬆ back to top](#table-of-contents)**



## Referenti Arka

| Nome                  | Sfera di influenza      |
|-----------------------|-------------------------|
| Nicolò Tacconi | Hubservices Vita, Albedino     |
| Daniele Campanini | * |
| Mattia D'Annibale | Arkafriend FE, Arkafriend BE, Albedino |
| Federico Marchi   | Arka4U, Hubservices Anagrafiche |
| Matteo Facchin    | Faro, FEBV |
| Timoty Granziero  | Albedino, MyArka |

**[⬆ back to top](#table-of-contents)**



## Fonti
- https://refactoring.guru
- https://en.wikipedia.org

**[⬆ back to top](#table-of-contents)**

# Code-Quality-Gate

## Table of Contents
1. [Premesse](#premesse)
1. [Code Quality Metrics](#code-quality-metrics)
    1. [Cyclomatic Complexity](#cyclomatic-complexity)
    1. [Single Responsibility Principle](#single-responsibility-principle)
1. [Testing](#testing)

## Bozze

- Nomi test uguali a nomi classi
- Porte divise per concetti e non per "adapter"
  - Esempio | **BAD** una porta unica per adapter di tutti i metodi della stessa fonte | **GOOD** una porta per concetto A, una porta per concetto B, ecc.. |)
- Massimo 5 dipendenze
- Quando lo UseCase diventa troppo grande lo possiamo spezzare in più classi. Il nome di tali classi avrà il suffisso “…Auxiliary”. Queste classi ausiliarie hanno lo stesso ruolo degli use case (quindi compongono varie porte), servono solo a spezzare il codice dello use case

## Premesse

- È possibile sottomettere codice che non rispetti le [Code Quality Metrics](#code-quality-metrics) riportate ma dev'essere condiviso in code review con un referente Arka sulla qualità, tramite PR o code review sincrona.
- In caso non venissero rispettate, le metriche e convenzioni di coding riportate nel presente documento possono essere utilizzate per rigettare o fare revert del codice pubblicato.
- In futuro potrebbe venire integrato qualche strumento di static code analysis come [SonarQube](https://www.sonarqube.org/).

## Code Quality Metrics

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

### Single Responsibility Principle

**[⬆ back to top](#table-of-contents)**

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

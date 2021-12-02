# Code-Quality-Gate

* In caso non venissero rispettate, le metriche e convenzioni di coding riportate nel presente documento possono essere utilizzate per rigettare o fare revert del codice pubblicato.
* In futuro potrebbe venire integrato qualche strumento di static code analysis come SonarQube

---

## Bozza

* Nomi test uguali a nomi classi
* Porte divise per concetti e non per "adapter"
  * Esempio | **BAD** una porta unica per adapter di tutti i metodi della stessa fonte | **GOOD** una porta per concetto A, una porta per concetto B, ecc.. |)
* Massimo 5 dipendenze
* Quando lo UseCase diventa troppo grande lo possiamo spezzare in più classi. Il nome di tali classi avrà il suffisso “…Auxiliary”. Queste classi ausiliarie hanno lo stesso ruolo degli use case (quindi compongono varie porte), servono solo a spezzare il codice dello use case

## Testing

### Test unitari
Per scrivere un test unitario chiaro, vanno rispettate alcune regole:

Le sezioni _Arrange_, _Act_ e _Assert_ devono essere separate da una riga vuota
* **Arrange**: Sezione in cui si inizializzano gli input dei test ed eventuali mock. Input e mock vanno separati da una riga bianca
* **Act**: Sezione in cui viene chiamato il metodo che si sta testando
* **Assert**: sezione in cui si effettuano le assert

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

### Preferire AssertJ a JUnit o Hamcrest per le asserzioni

```java
// GOOD
import static org.assertj.core.api.Assertions.assertThat;
 
assertThat(result).isEqualTo("value");
 
// BAD
import static org.junit.assertThat;
 
assertThat(result, Matchers.is("value"));
assertEquals("value", result);
```

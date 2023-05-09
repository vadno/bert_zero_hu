# bert_zero_hu

Ebben a kísérletben a [huBERT](https://hlt.bme.hu/en/resources/hubert) modellt finomhangoltam a zérónévmások beillesztésének a feladatára.
A tanító- és a kiértékelőanyagot a [KorKor korpusz](https://github.com/vadno/korkor_pilot) alapján készítettem.
A finomhangolást az alábbi [tutorial](https://github.com/NielsRogge/Transformers-Tutorials/blob/master/BERT/Custom_Named_Entity_Recognition_with_BERT_only_first_wordpiece.ipynb) alapján készítettem.

## A feladat

A zérónévmások beillesztését címkézési feladatként kezeltem.
Az alábbi címkék szerepelnek a tokenek mellett:
* `NOZERO`: a tokenhez nem tartozik zérónévmás
* `SUBJ`: zéró alany tartozik hozzá
* `OBJ`: zéró tárgy tartozik hozzá
* `SUBJOBJ`: zéró alany és tárgy is tartozik hozzá
* `POSS`: zéró birtokos tartozik hozzá

## Tanítóanyag

A [korpusz](/korkor_sents.txt) tartalmazza a zérónévmásokat a fent ismertetett címkék formájában.
A tanításhoz a korpusz random kiválasztott 80%-át használtam.

## Finomhangolás

A finonhangolás folyamatát az alábbi Google Colab [munkafüzetben](https://colab.research.google.com/drive/1LGMTvdQJKMq64Q9Al2C2T7ejMAH53Vta?usp=sharing) végeztem el.

## Modell

A finomhangolás folyamatát a fent ismertetett munkafüzetben végig lehet vezetni.
A létrejövő szótár, a súlyok és a modell konfigurációs fájlja a [`transformers`](https://huggingface.co/docs/transformers/index) `from_pretrained()` metódusával betölthető és használható.
A modell [innen](https://nlp.nytud.hu/bertzerohu/) letölthető.

## Kiértékelés

A kiértékeléshez a korpusz 20%-át használtam.
A kiértékelés során a finomhangoláskor megadott 1 epoch után a training accuracy 0,96, a validation accuracy pedig 0,98 lett.
A tokenizálás eredményeként kapott subword tokenek visszaállítása után az egyes tokenekhez tartozó első subword tokenhez kapott címkét vettem figyelembe a kiértékelés során.
A négy címkére külön számítottam pontosságot, fedést és F-mértéket úgy, hogy az egyes címkék esetében az alábbi találati kategóriákba soroltam a modell által kibocsátott címkéket összevetve a tesztadatban szereplő gold standard címkékkel:

* valós pozitív: a jósolt címke és a gold standard címke megegyezik
* álpozitív: a jósolt címke eltér a gold standard címkétől (és nem `NOZERO`)
* álnegatív: a jósolt címke `NOZERO`
* valós negatív: minden egyéb esetben

|              | pontosság | fedés |  F-1 |
|-------------:|----------:|------:|-----:|
|       `SUBJ` |      1,00 |  0,96 | 0,98 |
|        `OBJ` |      0,33 |  1,00 | 0,50 |
|    `SUBJOBJ` |      0,73 |  1,00 | 0,84 |
|       `POSS` |      1,00 |  0,94 | 0,97 |
| `összesítve` |      0,97 |  0,96 | 0,96 |
|      `átlag` |      0,68 |  0,72 | 0,69 |

A modell az `OBJ` címkék esetében teljesített a leggyengébben, azonban szem előtt kell tartani, hogy a tesztadat 260 mondatában összesen 9 `OBJ` címke szerepelt.
A leggyakoribb címkéből (`SUBJ`) 188 szerepelt a tesztadatban, amelyeket nagy pontossággal tudott azonosítani a modell, igaz, a fedése nem volt tökéletes.
Az összesített eredmények esetében a címkénként kapott valós pozitív/negatív és álpozitív/álnegatív találatokat összesítettem, majd ezekre számoltam pontosságot, fedést és F-mértéket.
A négyféle címkére kapott pontosság, fedés és F-mérték átlaga az `OBJ` címkéknél mért gyenge eredményeknek köszönhetően ilyen alacsony.

## Licenc

GNU General Public License v3.0

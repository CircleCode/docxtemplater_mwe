# Résumé

-   Le template est [`template.docx`](template.docx)
-   Le résultat est [`generated.docx`](generated.docx)
-   Le résultat après ouverture puis sauvegarde sous Word est [`generated2.docx`](generated2.docx)
-   Les données sont
    
    ```json
    {
      "dfecart_actions_decidees": "<p>foo<\/p>",
      "dfecart_delai_mise_oeuvre": "<p>bar<\/p>"
    }
    ```

Le fichier `generated.docx` est malformé sous libreOffice alors que `generated2.docx` est correctement formé.

# Analyse

1.  On décompresse les docx dans des répertoires distincts, puis on linte le fichier `word/document.xml` (dans `document_lint.xml`) pour plus de lisibilité
1.  En comparant [`template/document_lint.xml`](template/document_lint.xml) et [`generated/document_lint.xml`](generated/document_lint.xml),
    on constate que la balise html a été remplacée par
    ```xml
    <w:pPr>
        <w:u w:val="single"/>
        <w:pStyle w:val="TextBody"/>
        <w:rPr/>
    </w:pPr>
    <w:r>
        <w:rPr/>
        <w:t xml:space="preserve">foo</w:t>
    </w:r>
    ```
1.  En comparant [`generated/document_lint.xml`](generated/document_lint.xml) et [`generated2/document_lint.xml`](generated2/document_lint.xml),
    on constate que les balises
    ```xml
    <w:pPr>
        <w:u w:val="single"/>
        <w:pStyle w:val="TextBody"/>
        <w:rPr/>
    </w:pPr>
    <w:r>
        <w:rPr/>
        <w:t xml:space="preserve">foo</w:t>
    </w:r>
    ```
    ont été remplacées par
    ```xml
    <w:r>
        <w:t>foo</w:t>
    </w:r>
    ```
1.  enfin, en supprimant la chaîne `<w:pPr><w:u w:val="single"/><w:pStyle w:val="TextBody"/><w:rPr/></w:pPr>` du fichier `word/document.xml`,
    le word correspondant est correctement interprété.

# Conclusion

Il semble donc que les balises

```xml
<w:pPr>
    <w:u w:val="single"/>
    <w:pStyle w:val="TextBody"/>
    <w:rPr/>
</w:pPr>
```

bien que supportées par Word, puissent poser problème (d'ailleurs, Word les retire lui-même du document…).

# Notes :

-   Les conversions ont été effectuées depuis la page https://docxtemplater.com/demo/#html afin d'avoir une configuration la plus standard possible
-   Ce bug n'était pas constaté avec la version 3.6.6 du module html
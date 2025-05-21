

 Λειτουργικότητα που προσφέρει το σύστημα:

- Δημιουργία ευρετηρίου από σύνολο ειδησεογραφικών άρθρων (CSV).
- Αναζήτηση λέξεων-κλειδιών στα πεδία `body`, `headline`, `description`.
- Περιορισμένη αναζήτηση πεδίου (π.χ. `headline:olympics`).
- Σελιδοποίηση αποτελεσμάτων (10 ανά σελίδα με `n`/`p`).
- Αλφαβητική ταξινόμηση τίτλων (`sort`).
- Ιστορικό αναζητήσεων (20 τελευταίες queries).
- Εξάλειψη διπλών URLs ανά σελίδα.
- Τονισμός όρων με ANSI-bold highlighting.

Οδηγίες Χρήσης:

Διαθέσιμες εντολές:
- `<query>`: νέα αναζήτηση
- `n` / `p`: επόμενη / προηγούμενη σελίδα
- `sort`: ταξινόμησης
- `history`: εμφάνιση ιστορικού
- `exit`: έξοδος

Δομή Κώδικα

src/main/java/com/example/search/
 Indexer.java    # Δημιουργεί το Lucene index από CSV
	βοηθητικές κλάσεις:

	Constructor Indexer(String indexDir):
		Χτίζει FSDirectory και IndexWriter με EnglishAnalyzer, ανοίγει/δημιουργεί το ευρετήριο.

	Method void close():
		Κλείνει τον IndexWriter (αποθηκεύει και κλείνει το ευρετήριο).
	
	Method void indexDirectory(Path docsDir):
		Διάσχιση φακέλου (walkFiles), ανάγνωση κάθε αρχείου κειμένου, δημιουργία Document με πεδία path (StringField) και body (TextField), και πρόσθεση στο ευρετήριο.

	Method void indexCSV(Path csvFile):
		Ανάγνωση CSV μέσω Commons-CSV, μέχρι 1.000 εγγραφές, μετατροπή κάθε εγγραφής σε Lucene Document με πεδία id, author, category, section, date, url, headline, description, keywords, secondHeadline, body, και SortedDocValuesField για headline.
	
	Static Main public static void main(String[] args):
		Δεσμεύει ορίσματα indexDir και docsDirOrCsv, δημιουργεί Indexer, και καλεί είτε indexDirectory είτε indexCSV ανάλογα αν το μονοπάτι είναι φάκελος ή αρχείο .csv.
 	
Searcher.java   # Αναζήτησης και εμφάνιση αποτελεσμάτων
        βοηθητικές κλάσεις:

	Constructor Searcher(String indexDir):
		Ανοίγει τον κατάλογο ευρετηρίου με DirectoryReader και FSDirectory.
		Δημιουργεί IndexSearcher και ρυθμίζει MultiFieldQueryParser με EnglishAnalyzer.

	Method SearchResult searchWithQuery(String q, int topN, boolean sortByTitle):
		Διαβάζει το String q και το μετατρέπει σε Lucene Query.
		Εκτελεί αναζήτηση με ή χωρίς ταξινόμηση SortField ανά headline.
		Επιστρέφει SearchResult που περιέχει το Query και τα TopDocs.

	Method void displayPage(SearchResult result, int page):
		Εκτελεί σελιδοποίηση (10 hits/page) πάνω στο αποτέλεσμα.
		Φιλτράρει διπλά URLs και εμφανίζει μόνο το πρώτο.
		Δημιουργεί Highlighter με ANSI-bold formatting και εμφανίζει τονισμένα τα matched terms στο headline και στο body snippet.

	Method void close():
		Κλείνει τον IndexSearcher (κλείνει IndexReader).

	Static Main public static void main(String[] args):
		Διαχειρίζεται το loop: εισαγωγή ερωτήματος ή εντολών n,p,sort,history,exit.
		Καλεί searchWithQuery, displayPage και διατηρεί και εμφανίζει το ιστορικό τελευταίων 20 ερωτημάτων.




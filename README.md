# MemonomenaPeristatika.gr
Μεμονωμένα περιστατικά αστυνομικής βίας

# Άδεια χρήσης
Τα δεδομένα υπάγονται στην άδεια χρήσης [Creative Commons 4.0 Attribution Non-Commercial Share-Alike](https://creativecommons.org/licenses/by-nc-sa/4.0/). Αν θέλετε να τα χρησιμοποιήσετε για μη εμπορικούς σκοπούς όπως δημοσιογραφία, μπορείτε να το κάνετε ελεύθερα, αρκεί να αναφερθεί η πηγή των δεδομένων ως MemonomenaPeristatika.gr. Ο κώδικάς μας υπάγεται στην άδεια χρήσης [GPL 3.0](https://www.gnu.org/licenses/gpl-3.0.en.html).

# Αρχιτεκτονική λογισμικού
Το κομμάτι του front-end είναι γραμμένο σε next.js χρησιμοποιώντας static site generation και ζει στο φάκελο `app/`. Οποιεσδήποτε αλλαγές στη μορφοποίηση ή στο στατικό περιεχόμενο της σελίδας (SCSS, header/footer κλπ.) γίνονται εκεί.

Για να δοκιμάσουμε τις αλλαγές μας στο `app/`:
1. cd app.
2. Εγκαθιστούμε τα dependencies με `npm install`.
3. Τρέχουμε `npm run dev` και το αφήνουμε να τρέχει.
4. Ανοίγουμε έναν browser στο τοπικό URL που θα μας δώσει.
5. Κάνουμε ό,τι αλλαγές θέλουμε. Ο browser κάνει αυτόματα refresh.

Για να παράξουμε production build του `app/` τρέχουμε `npm run build`. Αν θέλουμε να παράξουμε ένα στατικό production build του app τρέχουμε `next export` (θα χρειαστεί να κάνουμε `npm install -g next`) και το αποτέλεσμα είναι στο `app/out`. Σε περίπτωση που το compilation δε δουλέψει λόγω cached αρχείων του build, μπορούμε μέσα στο `app` να κάνουμε `rm -rf .next out` και να ξαναδοκιμάσουμε.

Το next.js app μας γίνεται host στο vercel. Το domain memonomenaperistatika.gr έχει nameservers στο papaki. Τα A records στο papaki (τόσο για το @ όσο και για το www) δείχνουν στους servers της vercel. Κάθε φορά που κάνουμε push στο main branch, το vercel κάνει αυτόματα deploy το next.js app μας.

Το next.js app μας κάνει το static site generation χρησιμοποιώντας ένα αρχείο `entries.csv` και ένα αρχείο cache. Το `entries.csv` κατεβαίνει manually μία στο τόσο κάνοντας export από το Google Sheets. Το αρχείο cache περιέχει μία συσχέτιση ανάμεσα σε entries από το αρχείο entries.csv και σε self-hosted URLs από videos και thumbnails. Το next.js app μόνο διαβάζει (δεν γράφει) από αυτά τα αρχεία.

Η παραγωγή του cache από το `entries.csv` γίνεται από το generator script που ζει στο φάκελο `generator/`. Είναι γραμμένο σε node.js. Διαβάζει το αρχείο `entries.csv` και κοιτάει για εγγραφές που είναι μαρκαρισμένες ως "approved". Για κάθε εγγραφή, κατεβάζει το αντίστοιχο video από το source website (π.χ. YouTube, Facebook, Twitter) χρησιμοποιώντας το `youtube-dl`. Στη συνέχεια ανεβάζει το αντίστοιχο video στους self-hosted providers μας που αυτή τη στιγμή είναι το AWS S3 και το Cloudinary. Από το AWS S3 παίρνει το videoURL, ενώ από το Cloudinary παίρνει το thumbnailURL τα οποία στη συνέχεια γράφει στο cache αρχείο για χρήση από το next.js app.

# Τρόπος λειτουργίας
* Οι χρήστες υποβάλλουν (ανώνυμα) περιστατικά μέσω μιας Google Forms, που πηγαίνουν σε ένα Google Sheets.
* Οι moderators μπορούν να κάνουν αλλαγές στα περιστατικά κάνοντας αλλαγές στο Google Sheets, ώστε να έχουμε ομοιομορφία στους τίτλους, να μην έχουμε λινκς σε βίντεο-ρεπορτάζ από κανάλια κτλ.
* Σε τακτική βάση, κατεβάζουμε το Google Sheets σαν ένα CSV (με το όνομα "entries.csv") και τρέχουμε το script στον φάκελο `generator`, που:
  1. Κατεβάζει και ανεβάζει όλα τα βίντεο στο AWS cloud μας.
  2. Παράγει ένα αρχείο cache που περιέχει video URLs και thumbnail URLs.
  3. Κάνουμε commit το νέο entries file και το cache file.

# Συνεισφορές
* Αν θέλεις να προσθέσεις ένα περιστατικό, κάνε χρήση [της φόρμας](https://forms.gle/cNgRuEyUQWDPr4rr8) από το webpage. Παρακαλώ μην ανοίγετε PRs για προσθήκη περιστατικών, καθώς η σελίδα είναι auto-generated.
* Μην αλλάζετε το αρχείο `entries.csv` ή το αρχείο cache, παράγονται αυτόματα.
* Αν θέλεις να κάνεις στυλιστικές αλλαγές, μπορείς να ανοίξεις ένα PR εδώ, αλλάζοντας τον react κώδικα στο φάκελο `app/`.
* Για μεγαλύτερες αλλαγές ή ιδέες, άνοιξε ένα issue για συζήτηση.

# Moderation
Η ενότητα αυτή αφορά τους moderators. Οι moderators ασχολούνται με την εξέταση της εγκυρότητας των καταχωρήσεων που στέλνουν οι χρήστες μας.

## Καταχωρήσεις
Οι αναφορές από τους χρήστες μας συλλέγονται σε ένα Google Spreadsheet. Αν είσαι moderator θα πρέπει να σου έχει δοθεί πρόσβαση
σε αυτό το spreadsheet με ένα link. Προσοχή, αυτό το link είναι μόνο δικό σου και δεν πρέπει να το μοιραστείς με άλλους! Αν το
δώσεις σε άλλους θα έχουν τη δυνατότητα να αλλάξουν τα δεδομένα μας.

Το Spreadsheet γεμίζει αυτόματα όταν έρχονται αναφορές από το site. Κάθε νέα αναφορά προστίθεται σαν τελευταία γραμμή μέσα στο
spreadsheet. Οι γραμμές στο spreadsheet συνεπώς ρυθμίζονται αυτόματα και άρα θα πρέπει να προσέξουμε:

* Δεν διαγράφουμε υπάρχουσες γραμμές από το spreadsheet. Αν κάποια καταχώρηση είναι λανθασμένη, τη μαρκάρουμε ως "rejected" στη στήλη εγκυρότητας (βλέπε παρακάτω).
* Δεν προσθέτουμε νέες δικές μας γραμμές στο spreadsheet. Θα διαγραφούν αυτόματα όταν γίνει επόμενη καταχώρηση και θα χάσουμε τα δεδομένα μας!

Αν θέλουμε να προσθέσουμε δικές μας καταχωρήσεις, [χρησιμοποιούμε τη φόρμα](https://forms.gle/cNgRuEyUQWDPr4rr8) και όχι το spreadsheet.

## Στήλες

Στο Spreadsheet καταγράφονται οι εξής στήλες:

1. Η ημερομηνία και ώρα κατά την οποία έγινε η αναφορά (πότε δηλαδή πατήθηκε το link)
2. Πότε συνέβη το περιστατικό.
3. Πού συνέβη το περιστατικό.
4. Σύνδεσμος σε βίντεο.
5. Σύντομη περιγραφή.
6. Εγκυρότητα καταχώρησης.
7. Voice over.

Για τη στήλη εγκυρότητας ισχύουν τα εξής:

* Η εγκυρότητα κάθε καταχώρησης μαρκάρεται στην ειδικά διαμορφωμένη στήλη.
* Όταν γίνεται μία νέα καταχώρηση από χρήστη μας, η τελευταία στήλη είναι αρχικά κενή.
* Εμείς σαν moderators αλλάζουμε αυτή τη στήλη αφού εξετάσουμε το περιεχόμενο της καταχώρησης.
* Όσες καταχωρήσεις είναι έγκυρες μαρκάρονται ως "approved".
* Όσες καταχωρήσεις είναι λανθασμένες μαρκάρονται ως "rejected".
* Όσες καταχωρήσεις είναι διπλότυπες μαρκάρονται ως "duplicate".
* Όσες καταχωρήσεις είναι ασαφείς μαρκάρονται ως "unclear".
* Οι καταχωρήσεις που είναι "approved" θα εμφανιστούν στο website με την επόμενη ενημέρωση που θα κάνουμε.
* Οι καταχωρήσεις που χάσουν την εγκυρότητα "approved" θα αφαιρεθούν στο website με την επόμενη ενημέρωση που θα κάνουμε.

Ως moderators η δουλειά μας είναι αφενός να αλλάξουμε τα δεδομένα μέσα σε κάθε εγγραφή ώστε να ανταποκρίνονται στην πραγματικότητα και στο ύφος της σελίδας, αφετέρου να μαρκάρουμε την κάθε καταχώρηση ως "approved", "rejected", "duplicate" ή "unclear".

## Ύφος
Η πολιτική μας είναι να διατηρούμε το λόγο στο site όσο το δυνατόν ουδέτερο. Συνεπώς δεν χρησιμοποιούμε φορτισμένες λέξεις όπως "μπάτσοι" και "γουρούνια", ούτε χρησιμοποιούμε ειρωνικά εισαγωγικά όπως "<<προστατεύουν>>". Οι αναρτήσεις μας έχουν τη μορφή είδησης, και όχι άποψης, οπότε αντί για "Θρασύδειλη εισβολή σε πολυκατοικία" γράφουμε απλώς "Εισβολή σε πολυκατοικία". Η ουδετερότητα μάς επιτρέπει να έχουμε ευρύτερο κοινό και να επηρεάσουμε περισσότερες απόψεις, χωρίς να απευθυνόμαστε μόνο στους ομοϊδεάτες μας. Άλλωστε, τα γεγονότα μιλούν από μόνα τους.

Ο τίτλος κάθε καταχώρησης θα πρέπει να είναι σε μορφή ουσιαστικού και όχι ρήματος. Για παράδειγμα, θα πρέπει να γράφει "Εξώθηση άνδρα από πολυκατοικία" αντί για "Μπάτσοι εξωθούν άνδρα από πολυκατοικία". Διατηρούμε σύντομους και περιεκτικούς τίτλους. Επιπλέον, ελέγχουμε την ορθογραφία μας. Οι τίτλοι θα πρέπει να ξεκινούν με κεφαλαίο γράμμα, ενώ τα υπόλοιπα γράμματα θα πρέπει να είναι μικρά (με εξαίρεση όπου είναι απαραίτητο για την ορθογραφία). Θα πρέπει να είναι στα ελληνικά (και όχι Greeklish) και να είναι σωστά τονισμένοι.

## Video
Κάθε καταχώρηση θα πρέπει να συνοδεύεται από ένα link σε βίντεο όπου θα φαίνεται το περιστατικό. Καταχωρήσεις χωρίς video δεν ανεβαίνουν. Αν μία καταχώρηση έχει μόνο κείμενο ή μόνο εικόνα, ψάχνουμε να βρούμε αν υπάρχει αντίστοιχο video και προσθέτουμε το σύνδεσμο στο αντίστοιχο κελί.

Επειδή πολλοί πάροχοι (π.χ. YouTube, Facebook, Twitter) λογοκρίνουν το περιεχόμενο, η πλατφόρμα μας αντιγράφει και αποθηκεύει τα videos που αναρτώνται. Αυτό σημαίνει ότι σε περίπτωση που το περιεχόμενο κρυφτεί για λόγους βίας, θα συνεχίσει να εμφανίζεται και να παίζει στο site μας. Επιπλέον, σε περίπτωση που ένα video διαγραφεί από τον πάροχο ή το χρήστη που το ανέρτησε, η σελίδα μας θα συνεχίσει να το έχει. Αυτό σημαίνει ότι καταχωρήσεις που έγιναν "approved" όταν το video τους ήταν έγκυρο και στη συνέχεια κατέβηκε μπορούν να συνεχίσουν να είναι "approved" και δεν χρειάζεται να αφαιρεθούν.

Τα video που αναρτούμε δεν περιέχουν voice-over ή άλλο πολιτικό σχολιασμό. Σε περίπτωση που κάποιο video έχει, αναζητούμε εναλλακτικό video του ίδιου περιστατικού χωρίς voice-over. Μόνο στην περίπτωση που δεν υπάρχει άλλη επιλογή, κρατάμε το video με το voice-over και μαρκάρουμε "yes" στη στήλη με τίλτο "Voice over". Αν αργότερα εμφανιστεί διπλότυπη καταχώρηση με video του ίδιου περιστατικού χωρίς voice over, κάνουμε "rejected" την παλιά ανάρτηση και "approved" τη νέα, με το video χωρίς voice-over.

## Διπλότυπες καταχωρήσεις
Δεν αναρτούμε το ίδιο περιστατικό δύο φορές, ακόμα και αν υπάρχουν πολλαπλά διαφορετικά video που το δείχνουν. Όταν εξετάζουμε μία νέα καταχώρηση, ελέγχουμε αν υπάρχουν διπλότυπα. Διπλότυπο μπορεί να σημαίνει ότι κάποιος ανέβασε ακριβώς το ίδιο video URL με κάποιον άλλον, αλλά μπορεί να σημαίνει και ότι ανέβασε ένα διαφορετικό link στο ίδιο video ή ένα άλλο video από το ίδιο περιστατικό. Σε κάθε περίπτωση, μαρκάρουμε την καταχώρηση ως "duplicate". Για να εντοπίσουμε διπλότυπα, ελέγχουμε αν το URL είναι το ίδιο με κάποιο άλλο, καθώς και παρόμοιους τίτλους και ημερομηνίες.

## Μεταδεδομένα
Τα μεταδεδομένα της κάθε καταχώρησης είναι η ημερομηνία και ο τόπος που συνέβη το περιστατικό. Σε περίπτωση που αυτά τα στοιχεία λείπουν, τα αναζητούμε και τα συμπληρώνουμε. Για την ημερομηνία, ο πιο εύκολος τρόπος είναι να δούμε την ημερομηνία ανάρτησης του video και την περιγραφή του. Αν τα μεταδεδομένα είναι ασαφή, μπορούμε να αναζητήσουμε πληροφορίες για το περιστατικό στον τύπο.

Η ημερομηνία καταχώρησης (πρώτη στήλη) αφορά την ημερομηνία που ο χρήστης μας έστειλε το περιστατικό του σε εμάς και περιέχουν ημερομηνία και ώρα. Αυτή την στήλη δεν την πειράζουμε.

Η ημερομηνία που συνέβη το περιστατικό (τρίτη στήλη) δηλώνεται από τους χρήστες και εμφανίζεται στο spreadsheet στη μορφή μήνας/ημέρα/έτος. Αν θέλουμε να αλλάξουμε αυτή την ημερομηνία θα πρέπει κι εμείς να την καταχωρήσουμε στη μορφή μήνας/ημέρα/έτος (π.χ. 12/31/2020 για 31 Δεκεμβρίου 2020). Το site στη συνέχεια την επεξεργάζεται και την εμφανίζει στα Ελληνικά (στη μορφή 31 Δεκεμβρίου 2021). Οι τόποι αναγράφονται στα Ελληνικά και μπορεί να είναι είτε πόλεις όπως "Αθήνα", είτε γειτονιές όπως "Νέα Σμύρνη", είτε πανεπιστήμια όπως "ΑΠΘ". Αποφεύγουμε πολύ συγκεκριμένες τοποθεσίες όπως "Αντιγονιδών & Σούτσου γωνία".

## Αποφεύγοντας τη διπλή δουλειά
Ενδέχεται εσύ και άλλοι moderators να δουλεύετε πάνω στο spreadsheet ταυτόχρονα. Γι' αυτό το λόγο, όταν εργάζεσαι σε μία καταχώρηση, να επιλέγεις με το ποντίκι σου το κελί κάτω από τη στήλη εγκυρότητας στην γραμμή που εργάζεσαι. Αυτό θα χρωματίσει το κελί στις οθόνες όλων των άλλων moderators που μπορεί να βλέπουν το spreadsheet την ίδια στιγμή. Αν σε κάποια γραμμή το κελί της στήλης εγκυρότητας είναι χρωματισμένο από κάποιον άλλον, αυτό σημαίνει ότι εκείνος εργάζεται σε αυτή τη γραμμή, οπότε επίλεξε μία άλλη γραμμή να δουλέψεις για να μην χρειαστεί να γίνει διπλή δουλειά.

## Βήματα
Η διαδικασία για να κάνουμε moderation είναι η εξής:

1. Βρίσκουμε κάποια γραμμή που στην στήλη εγκυρότητας να είναι κενή. Ελέγχουμε ότι δεν εργάζεται κάποιος άλλος στη γραμμή μας.
2. Κάνουμε κλικ στο κελί της εγκυρότητας εντός της γραμμής που πρόκειται να ελέγξουμε.
3. Ελέγχουμε ότι η καταχώρηση δεν είναι διπλότυπη.
4. Ελέγχουμε ότι υπάρχει έγκυρο link για video και δεν έχει κατέβει από τον πάροχο ή τον ιδιοκτήτη του.
5. Βλέπουμε το video και ελέγχουμε ότι ανταποκρίνεται στον τίτλο. Σε περίπτωση που δεν είναι σαφές τι συνέβη, μαρκάρουμε την καταχώρηση ως "unclear". Το "approved" γίνεται μόνο για σαφή περιστατικά αυθαιρεσίας.
6. Ελέγχουμε και διορθώνουμε τη μορφή και ύφος του τίτλου.
7. Ελέγχουμε και διορθώνουμε ή συμπληρώνουμε τα μεταδεδομένα του συμβάντος.
8. Ελέγχουμε ότι το video δεν περιέχει voice-over.
9. Αν όλα είναι εντάξει, μαρκάρουμε την εγκυρότητα ως "approved".

Επειδή τα περιστατικά είναι σκληρά, φροντίστε να κάνετε διαλείμματα όσο κάνετε moderation. Ευχαριστούμε για τη συμβολή σας.

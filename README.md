# Πρώτο παραδοτέο στο εργαστήριο Αρχιτεκτονικής Υπολογιστών

**Σταύρος Πουνιώτης 8995** / Παρούσης Σταύρος Δήμος, Εργαστήριο Β Ομάδα 17

### 1ο ερώτημα

Στο πρώτο ερώτημα μας ζητείται να κατάγραψουμε τα βασικά χαρακτηριστικά του συστήματος χρησιμοποιώντας το αρχείο _starter_se.py_.  

* Όσων αφορά τον τύπο του επεξεργαστή, βλέπουμε στο αρχείο  _starter_se.py_ ότι μπορούμε να επιλέξούμε έναν απο τους τρείς τύπους (_atomic_,_minor_,_hpi_). Μπορούμε να καταλάβουμε ποιόν τύπο επεξεργαστή χρησιμοποιήσαμε βλέποντας τα arguments που δώσαμε στην εντολή _build_.  

  >$ ./build/ARM/gem5.opt -d hello_result configs/example/arm/starter_se.py **--cpu="minor"** "tests/test-progs/hello/bin/arm/linux/hello"

     Βλέπουμε ότι έχουμε επιλέξει **τύπο επεξεργαστή = Minor**.

Στην συνέχεια μπορούμε να εντοπίσουμε πολλά χαρακτηριστικά από την συνάρτηση **_main()_**

* **Αριθμός πυρήνων**, στο παράδειγμα μας σύμφωνα με τα arguments έχουμε την default τιμή , **number of cores = 1**

  > parser.add_argument("**--num-cores**", type=int, **default=1**, help="Number of CPU cores")  

* **Συχνότητα λειτουργίας επεξεργαστή**, στο παράδειγμα μας σύμφωνα με τα arguments έχουμε την default τιμή, **cpu frequency = 4Ghz**

  > parser.add_argument("**--cpu-freq**", type=str, **default="4GHz**")

* **Τύπος μνήμης**, στο παράδειγμα μας σύμφωνα με τα arguments έχουμε την default επιλογή, **memory type = DDR3_1600_8x8** 

  >parser.add_argument("**--mem-type , default="DDR3_1600_8x8**", choices=ObjectList.mem_list.get_names(), help = "type of memory to use")

* **Κανάλια μνήμης**, στο παράδειγμα μας σύμφωνα με τα arguments έχουμε την default τιμή, **memory channels =  2**

  >parser.add_argument("**--mem-channels**", type=int, **default=2**, help = "number of memory channels")

* **Μέγεθος φυσικής μνήμης**, στο παράδειγμα μας σύμφωνα με τα arguments έχουμε την default τιμή, **memory size = 2GB**

  >parser.add_argument("**--mem-size**", action="store", type=str, **default="2GB"**, help="Specify the physical memory size")

Στην συνέχεια μπορούμε να εντοπίσουμε κάποια ακόμα χαρακτηριστικά από την συνάρτηση **init()**

* Μπορούμε να δούμε ότι το σύστημα έχει **δύο cache lines** επειδή οι _MinorCPUs_ λειτουργούν σε  **timing mode**

  >if self.cpu_cluster.memoryMode() == "timing":
  >self.cpu_cluster.addL1()
  >self.cpu_cluster.addL2(self.cpu_cluster.clk_domain)
  >self.cpu_cluster.connectMemSide(self.membus)

* **Το μέγεθος της γραμμής cache = 64 bytes**

  > cache_line_size = 64

* Τέλος βλέπουμε ότι το σύστημα τροφοδοτείται από μια **τάση = 3.3V** και έχει ένα ρολόι με **συχνότητα = 1GHz** 

  >self.voltage_domain = VoltageDomain(voltage="3.3V")
  >self.clk_domain = SrcClockDomain(clock="1GHz",
  >                          voltage_domain=self.voltage_domain)


### 2ο ερώτημα

#### α) 

Στο αρχείο **_config.ini_** μπορούμε να επαληθεύσουμε τα παρακάτω χαρακτηριστικά: 

* **μέγεθος γραμμής cache**

  > [system]
  >
  > cache_line_size=64

* **μέγεθος φυσικής μνήμης**

  > [system]
  >
  > cache_line_size=64

* **τύπος CPU**

  > [system.cpu_cluster.cpus]
  >
  > type=MinorCPU

* **τάση τροφοδοσίας**

  >[system.voltage_domain]
  >type=VoltageDomain
  >eventq_index=0
  >voltage=3.3

* **clock domain, ρολόι**

  > [system.clk_domain]  
  >
  > type=SrcClockDomain
  > clock=1000

* **cpu clock** στο cpu.cluster

  > [system.cpu_cluster.clk_domain]
  > type=SrcClockDomain
  > clock=250

#### β)

Ο αριθμός των committed instructions είναι **5028** και μπορούμε να το δούμε στο _stats.txt_ του _helloOut_

> sim_insts                                        **5028**                       # Number of instructions simulated



#### γ)

Ο αριθμός των προσπελάσων της L2 cache είναι **332** και μπορούμε να το δούμε στο _stats.txt_ του _helloOut_

> system.cpu_cluster.l2.overall_accesses::.cpu_cluster.cpus.inst          **332**    # number of overall (read+write) accesses



### 3ο ερώτημα

Σύμφωνα με το [documentation](https://www.gem5.org/documentation/) του gem5, τα μοντέλα in-order cpu που χρησιμοποιεί ο gem5 είναι  ο [SimpleCPU](https://www.gem5.org/documentation/general_docs/cpu_models/SimpleCPU) και ο [MinorCPU](https://www.gem5.org/documentation/general_docs/cpu_models/minor_cpu). 

Όλοι οι τύποι cpu κληρονομούν από την κλάση BaseCPU σχηματίζοντας την παρακάτω ιεραρχία :

- *BaseCPU*
  - BaseSimpleCPU
    - AtomicSimpleCPU
    - TimingSimpleCPU
  - MinorO3CPU
    - MinorCPU

Ουσιαστικά, έχουμε 3  in-order C++ μοντέλα CPU (ο O3 είναι out-of-order).  Atomic, Timing, και Minor. Όλα τα υπόλοιπα αποτελούν παραμετροποιήσεις αυτών των βασικών τύπων.

<u>**AtomicSimpleCPU**</u> : Το προεπιλεγμένο μοντέλο επεξεργαστή. Οι προσπελάσεις στην μνήμη γίνονται ακαριαία. Είναι ο γρηγορότερος από τους παραπάνω προσομοιωτές αλλά καθόλου ρεαλιστικός. Ιδιαίτερα χρήσιμος προκειμένου να *"bootαρει"* γρήγορα τα linux μέχρι ένα *checkpoint* και μετά *να αλλάξει με έναν πιο αργό επεξεργαστή*, προκειμένου να μετρηθούν κάποια *benchmark*.

<u>**TimingSimpleCPU**</u> : Οι προσπελάσεις μνήμης είναι ρεαλιστικές αλλα ο επεξεργαστής δεν έχει pipeline. Η προσομοίωση είναι γρήγορη αλλά πιο αργή από τον AtomicSimple. Οι caches κάνουν μεγάλη διαφορά και οδηγούν σε γρηγορότερους χρόνους προσπέλασης μνήμης.

<u>**MinorCPU**</u> : Αποτελεί ένα in-order μοντέλο με προκαθορισμένη διαδικασία διοχέτευσης αλλά προσαρμοζόμενες δομές δεδομένων και προσαρμοζόμενη συμπεριφορά εκτέλεσης.Το σταθερό pipeline του βοηθάει στην αναγνώριση και οπτικοποίηση μέσα σε αυτό της κάθε εντολής από μια προσομοίωση. Ο MinorCPU έχει επίσης υλοποιημένο έναν branch predictor που ενεργεί στο δεύτερο στάδιο του pipeline(Fetch2).

#### α)

Στη συνέχεια θα αναφέρομαι σε κάθε ξεχωριστό build που έχω αλλαγμένα τα χαρακτηριστικά του επεξεργαστή ως **_CaseX_** , όπου "**Χ**" αριθμός περίπτωσης. Ξεκινάμε από το **_Case1_** που έχει το default config του _se.py_ (cpu_clock = 2GHz και mem_type ="DDR3_1600_8x8").

Για το τρίτος μέρος γράψαμε το παρκάτω σκριπτάκι:

~~~c
#include <stdio.h>

int main() {    

    int i, number1 = 230, number2 = 12, sum = 0;

    
    for( i=1; i<100000; i++){

    // calculating sum

    sum = sum + (number1*i) + (number2*2*i);      

    }

    printf("result is %d", sum);

    return 0;

}
~~~

Τα αποτέλεσματα που προέκυψαν είνα τα εξής (**_Case1_**):

* MinorCPU: 0.001134 seconds
* TimingSimpleCPU: 0.003139 seconds

#### β)

Η διαφορά στους χρόνους οφείλεται στις απανωτές προσπελάσεις στη μνήμη που περιλαμβάνει το προγράμμα. H *pipelined* λογική του *MinorCPU* μοντέλου υπερτερεί έναντι του *TimingSimpleCPU* , ο οποίος μπλοκάρει την εκτέλεση περιμένοντας το αποτέλεσμα της προσπέλασης της μνήμης.

#### γ)

Αρχικά θα "πειράξω" τη συχνότητα του επεξεργαστή.

* **_Case2_** ( cpu_clock =4GHz και mem_type ="DDR3_1600_8x8" )
  * MinorCPU: 0.000581 seconds
  * TimingSimpleCPU: 0.001582 seconds
* **_Case3_**( cpu_clock = 1GHz και mem_type ="DDR3_1600_8x8" )
  * MinorCPU: 0.002241 seconds
  * TimingSimpleCPU: 0.006253 seconds

Όσων αφορά τις αλλαγές στο **cpu_clock** παρατηρώ ότι ο χρόνος προσομοιώσης (sim_seconds) είναι ανάλογος του **cpu_clock**, έτσι όταν παρατηρούμε ότι το **_Case2_** είναι δύο φορές ταχύτερο από το **_Case1_** και τέσσερις φορές ταχύτερο από το **_Case3_**.

Στη συνέχεια θα αλλάξω την τεχνολογίας της μνήμης.

* **_Case4_** ( cpu_clock = 2GHz και mem_type ="DDR4_2400_16x4")
  * MinorCPU: 0.001133 seconds
  * TimingSimpleCPU: 0.003139 seconds
* **_Case5_** ( cpu_clock = 2GHz και mem_type ="LPDDR2_S4_1066_1x32" )
  * MinorCPU: 0.001143 seconds
  * TimingSimpleCPU: 0.003147 seconds

Όσων αφορά τις αλλαγές στην μνήμη, δεν παρατήρησα μεγάλες διαφορές στους χρόνους,η DDR4 με την DDR3 είχαν απειροελάχιστη διαφορά. Στο **_Case5_** που χρησιμοποίησα την πιο αργή μνήμη που βρήκα παρατήρησα μια καθυστέρηση _0.01_ ms σε σχέση με τα **_Case1_** και **_Case4_**.



#### Πηγές

[gem5](https://www.gem5.org/documentation/)

[Memory Models](https://nitish2112.github.io/post/gem5-memory-model/)

[CPU Types](https://cirosantilli.com/linux-kernel-module-cheat/#gem5-cpu-types)

[General Info about gem5](http://learning.gem5.org/book/)



#### Κριτική

Θεωρώ πως η εργασία δεν ήταν ιδιαίτερα πολύπλοκη στην υλοποίηση της. Εγώ δυστυχώς δεν κατάφερα να απαντήσω σε όλα τα ερωτήματα που τέθηκαν γιατί δεν είχα αρκετό χρόνο. Πέρα από τα θέματα που αφορούν το γνωστικό αντικείμενο του μαθήματος, χαίρομαι που ασχολήθηκα με την εργασία καθώς μου έδωσε μια ώθηση να εγκαταστήσω ένα περιβάλλον ubuntu στο σκληρό μου (στο οποίο ελπίζω να συνεχίσω να δουλεύω περισσότερο μελλοντικά). Επίσης πήρα και μαι πρώτη γεύση από το github, με το οποίο δυστυχώς δεν είχα  ασχοληθεί μέχρι πρόσφατα. Τέλος, ήταν ιδιαίτερα ικανοποιητικό το να βλέπω το simulation να δουλεύει σωστά και να "τσεκάρω" στη συνέχεια τα στατιστικά, γενικά είναι ιδιαίτερα ενθαρρυντικό για όλους τους φοιτητές πιστεύω όταν βλέπουν απτά αποτελέσματα σε πράγματα που δημιουργούν.

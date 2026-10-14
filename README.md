import 'dart:async';
import 'dart:math';
import 'package:flutter/material.dart';
import 'package:supabase_flutter/supabase_flutter.dart';

final supabase = Supabase.instance.client;

Future<void> main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await Supabase.initialize(
    url: 'https://youqoymlxpwuwcxygamk.supabase.co',
    anonKey: 'ANON KEY NI SHU YERGA',
  );
  runApp(const ShartlarOyniApp());
}

class ShartlarOyniApp extends StatelessWidget {
  const ShartlarOyniApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      title: "Shartlar O'yini",
      theme: ThemeData(fontFamily: 'Roboto', brightness: Brightness.light),
      home: const SetupScreen(),
    );
  }
}

class AppGradients {
  static const LinearGradient primaryBg = LinearGradient(
    colors: [Color(0xFF8E2DE2), Color(0xFF4A00E0)],
    begin: Alignment.topLeft,
    end: Alignment.bottomRight,
  );
  static const LinearGradient cardBg = LinearGradient(
    colors: [Color(0xFFFFFFFF), Color(0xFFF3E7E9)],
    begin: Alignment.topCenter,
    end: Alignment.bottomCenter,
  );
  static const LinearGradient buttonGradient = LinearGradient(
    colors: [Color(0xFFFF416C), Color(0xFFFF4B2B)],
    begin: Alignment.centerLeft,
    end: Alignment.centerRight,
  );
  static const LinearGradient activePlayerGradient = LinearGradient(
    colors: [Color(0xFF00F2FE), Color(0xFF4FACFE)],
    begin: Alignment.topLeft,
    end: Alignment.bottomRight,
  );
  static const LinearGradient successBtn = LinearGradient(
    colors: [Color(0xFF11998E), Color(0xFF38EF7D)],
  );
  static const LinearGradient failBtn = LinearGradient(
    colors: [Color(0xFFED213A), Color(0xFF931010)],
  );
}

class Player {
  String name;
  int score;
  Player({required this.name, this.score = 0});
}

// Gradientli tugma (hamma joyda bir xil ishlatiladi)
Widget gradientButton({
  required String text,
  required LinearGradient gradient,
  required VoidCallback onTap,
  double height = 55,
  double fontSize = 18,
  double radius = 20,
}) {
  return GestureDetector(
    onTap: onTap,
    child: Container(
      width: double.infinity,
      height: height,
      decoration: BoxDecoration(
        gradient: gradient,
        borderRadius: BorderRadius.circular(radius),
        boxShadow: [
          BoxShadow(
            color: Colors.black.withOpacity(0.2),
            blurRadius: 12,
            offset: const Offset(0, 6),
          ),
        ],
      ),
      child: Center(
        child: Text(
          text,
          style: TextStyle(
            color: Colors.white,
            fontSize: fontSize,
            fontWeight: FontWeight.bold,
          ),
        ),
      ),
    ),
  );
}

// 1. O'yinchilarni kiritish ekrani
class SetupScreen extends StatefulWidget {
  const SetupScreen({super.key});

  @override
  State<SetupScreen> createState() => _SetupScreenState();
}

class _SetupScreenState extends State<SetupScreen> {
  final List<TextEditingController> _controllers = [];

  @override
  void initState() {
    super.initState();
    for (int i = 1; i <= 4; i++) {
      _controllers.add(TextEditingController(text: "O'yinchi $i"));
    }
  }

  @override
  void dispose() {
    for (var c in _controllers) {
      c.dispose();
    }
    super.dispose();
  }

  void _addPlayer() {
    if (_controllers.length < 10) {
      setState(() {
        _controllers.add(
          TextEditingController(text: "O'yinchi ${_controllers.length + 1}"),
        );
      });
    }
  }

  void _removePlayer(int index) {
    if (_controllers.length > 2) {
      setState(() {
        _controllers[index].dispose();
        _controllers.removeAt(index);
      });
    }
  }

  void _startGame() {
    List<Player> players = [];
    for (int i = 0; i < _controllers.length; i++) {
      String name = _controllers[i].text.trim();
      players.add(Player(name: name.isEmpty ? "O'yinchi ${i + 1}" : name));
    }
    Navigator.push(
      context,
      MaterialPageRoute(builder: (context) => GameScreen(players: players)),
    );
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Container(
        decoration: const BoxDecoration(gradient: AppGradients.primaryBg),
        child: SafeArea(
          child: Center(
            child: SingleChildScrollView(
              padding: const EdgeInsets.all(20.0),
              child: Container(
                padding: const EdgeInsets.all(24.0),
                decoration: BoxDecoration(
                  color: Colors.white,
                  borderRadius: BorderRadius.circular(30),
                  boxShadow: [
                    BoxShadow(
                      color: Colors.black.withOpacity(0.3),
                      blurRadius: 25,
                      offset: const Offset(0, 10),
                    ),
                  ],
                ),
                child: Column(
                  mainAxisSize: MainAxisSize.min,
                  children: [
                    ShaderMask(
                      shaderCallback: (bounds) => const LinearGradient(
                        colors: [Color(0xFFFF416C), Color(0xFF8E2DE2)],
                      ).createShader(bounds),
                      child: const Text(
                        "✨ SHARTLAR O'YINI ✨",
                        textAlign: TextAlign.center,
                        style: TextStyle(
                          fontSize: 26,
                          fontWeight: FontWeight.bold,
                          color: Colors.white,
                        ),
                      ),
                    ),
                    const SizedBox(height: 8),
                    const Text(
                      "Ismlarni kiriting va do'stlaringiz bilan bellashing!",
                      textAlign: TextAlign.center,
                      style: TextStyle(
                        color: Colors.grey,
                        fontSize: 13,
                        fontWeight: FontWeight.bold,
                      ),
                    ),
                    const SizedBox(height: 20),
                    ListView.builder(
                      shrinkWrap: true,
                      physics: const NeverScrollableScrollPhysics(),
                      itemCount: _controllers.length,
                      itemBuilder: (context, index) {
                        return Padding(
                          padding: const EdgeInsets.only(bottom: 10.0),
                          child: Row(
                            children: [
                              Expanded(
                                child: TextField(
                                  controller: _controllers[index],
                                  style: const TextStyle(
                                    fontWeight: FontWeight.bold,
                                  ),
                                  decoration: InputDecoration(
                                    labelText: "${index + 1}-O'yinchi",
                                    labelStyle: const TextStyle(
                                      color: Color(0xFF8E2DE2),
                                    ),
                                    filled: true,
                                    fillColor: const Color(
                                      0xFFF3E7E9,
                                    ).withOpacity(0.5),
                                    contentPadding: const EdgeInsets.symmetric(
                                      horizontal: 16,
                                      vertical: 12,
                                    ),
                                    border: OutlineInputBorder(
                                      borderRadius: BorderRadius.circular(16),
                                      borderSide: BorderSide.none,
                                    ),
                                    focusedBorder: OutlineInputBorder(
                                      borderRadius: BorderRadius.circular(16),
                                      borderSide: const BorderSide(
                                        color: Color(0xFFFF416C),
                                        width: 2,
                                      ),
                                    ),
                                    suffixIcon:
                                        _controllers[index].text.isNotEmpty
                                        ? IconButton(
                                            icon: const Icon(
                                              Icons.cancel,
                                              color: Colors.grey,
                                              size: 20,
                                            ),
                                            onPressed: () {
                                              setState(() {
                                                _controllers[index].clear();
                                              });
                                            },
                                          )
                                        : null,
                                  ),
                                  onChanged: (val) => setState(() {}),
                                ),
                              ),
                              if (_controllers.length > 2) ...[
                                const SizedBox(width: 8),
                                CircleAvatar(
                                  backgroundColor: const Color(0xFFFFE5E5),
                                  child: IconButton(
                                    icon: const Icon(
                                      Icons.delete,
                                      color: Colors.redAccent,
                                      size: 20,
                                    ),
                                    onPressed: () => _removePlayer(index),
                                  ),
                                ),
                              ],
                            ],
                          ),
                        );
                      },
                    ),
                    const SizedBox(height: 10),
                    if (_controllers.length < 10)
                      TextButton.icon(
                        onPressed: _addPlayer,
                        icon: const Icon(
                          Icons.add_circle,
                          color: Color(0xFF8E2DE2),
                          size: 22,
                        ),
                        label: const Text(
                          "O'yinchi Qo'shish",
                          style: TextStyle(
                            color: Color(0xFF8E2DE2),
                            fontWeight: FontWeight.bold,
                            fontSize: 15,
                          ),
                        ),
                      ),
                    const SizedBox(height: 15),
                    gradientButton(
                      text: "O'YINNI BOSHLASH 🚀",
                      gradient: AppGradients.buttonGradient,
                      onTap: _startGame,
                    ),
                  ],
                ),
              ),
            ),
          ),
        ),
      ),
    );
  }
}

// 2. Asosiy O'yin Ekrani
class GameScreen extends StatefulWidget {
  final List<Player> players;
  const GameScreen({super.key, required this.players});

  @override
  State<GameScreen> createState() => _GameScreenState();
}

class _GameScreenState extends State<GameScreen> {
  static const String _idleText = "Tugmani bosing va yorqin shartni oling!";

  int _currentPlayerIndex = 0;
  String _currentTask = _idleText;
  bool _taskActive = false;
  bool _timedOut = false;

  // Taymer
  Timer? _timer;
  int _secondsLeft = 0;

  // Bir marta chiqqan shart qayta chiqmaydi
  final Set<String> _usedTasks = {};
  final Random _random = Random();

  final Map<String, List<String>> _tasksData = {
    'etab1': [
      "O'ng tarafingizdagi insonga eng samimiy iltifot (kompliment) ayting.",
      " faqat qoshlaringizni qimirlatib turing.",
      "O'zingiz haqingizda hamma bilmaydigan 1 ta qiziqarli faktni aytib bering.",
      "O'zbekiston madhiyasidan 2 ta satr baland ovozda kuylang.",
      "Chap tarafingizdagi insonga qarab 10 soniya kulmasdan tikilib turing.",
      "Telefoningizdagi oxirgi tushgan rasmni hammasiga ko'rsating.",
      "Bitta oyoqda turib 15 soniya davomida 10 dan 1 gacha teskari sanang.",
      "Hozirgi xonada joylashgan 5 ta ko'k yoki qizil rangli narsani ayting.",
      "Shirinlik va tuzli ovqatni birgalikda yeyish haqida 1 daqiqa gapiring.",
      "Xonadagi eng chiroyli kiyingan odamni tanlang va sababini ayting.",
      "Bitta hayvon ovozini o'xshatib bering (boshqalar topishi kerak).",
      "Sekin harakat (Slow-motion) rejimida suv ichib ko'rsating.",
      "O'zingizning sevimli bolalik xotirangizni aytib bering.",
      "Stol ustiga barmoqlaringiz bilan qandaydir musiqa ritmini chaling.",
      "Oxirgi marta ko'rgan tushingizni qisqacha aytib bering.",
      "Xonadagi har bir insonga bittadan chiroyli so'z ayting.",
      "3 marta baland ovozda 'Men ushbu o'yin qiroliman!' deb ayting.",
      "Ismingizni havoda barmoq bilan yozib ko'rsating.",
      "Mashhur bir aktyor yoki xonandani taqlid qiling.",
      "O'zingizga 10 soniya davomida qarsak chalib tasalli bering.",
      "\"vov vov\" degan tovush bilan gapiring, lekin hech kimga tushuntirmang.",
    ],
    'etab2': [
      "30 soniya ichida 10 ta meva va sabzavot nomini tezkorlik bilan ayting.",
      "Tilni buramaydigan tezaytishni 3 marta ketma-ket aytib bering.",
      "O'zgacha (pingvincha yoki robotcha) usulda xonani bir aylanib chiqing.",
      "Xonadagi birorta buyumni olib, uni reklamadek sotib bering.",
      "Ko'zlaringizni yumib, yoningizdagi odamning qo'lini ushlab kimligini toping.",
      "1 dan 20 gacha faqat juft sonlarni ketma-ket ayting.",
      "O'zingizga tegishli bo'lmagan biror kiyimni vaqtincha kiyib turing.",
      "Ko'zlaringizni yumgan holda 15 soniya davomida bir oyoqda muvozanat saqlang.",
      "Hozirgi paytda mashhur bo'lgan trek qo'shiqdan 1 satr aytib bering.",
      "Xonadagi o'yinchilardan biriga xohlagan 1 ta savolingizni bering.",
      "Robotga o'xshab 30 soniya harakat qiling va gapiring.",
      "Telefoningizdagi eng oxirgi yuborilgan xabarni o'qib bering.",
      "Ko'zingizni yumgan holda mashina rasmini chizing.",
      "Pantomima usulida (gapirmasdan) birorta kasbni ko'rsating.",
      "Chap qo'lingiz bilan ismingizni qog'ozga yozing.",
      "5 ta turli mamlakat va ularning poytaxtini ayting.",
      " 5 ta sariq yoki qizil rangli narsani ko'rsating.",
      "Hayotingizdagi eng kulgili voqeani aytib bering.",
      "She'r shaklida o'zingizni tanishtiring.",
      "O'yin oxirigacha har bir gapingizdan keyin 'Xo'sh' deb qo'shib gapiring.",
      " faqat savol shaklida gapiring, javob bermang.",
    ],
    'etab3': [
      "15 ta otjimaniye (push-up) yoki 20 ta prisadaniya qiling.",
      "Hamma o'yinchilarga bir stakan suv quyib kelib bering.",
      "O'zingiz haqingizda 2 ta to'g'ri va 1 ta yolg'on fakt ayting, qolganlar yolg'onni topsin.",
      "Yoningizdagi o'yinchining portretini 1 daqiqada chizib bering.",
      "Telefonda istalgan kontaktga qo'ng'iroq qilib 'Men kosmosdaman' deb trubkani qo'ying.",
      " professional diktor kabi yangiliklar o'qing.",
      "Qo'llaringizni ishlatmasdan stoldagi biror narsani oling.",
      "Dramatik usulda va yig'lamsirab 'Menga muzqaymoq olib bering' deb ayting.",
      "O'yinchilardan birining soch turmagini yangicha usulda o'zgartirib qo'ying.",
      " umuman g'alati va tushunarsiz tilda gapiring.",
      "Operatorga o'xshab o'yinchilardan intervyu oling.",
      "O'zingiz orzu qilgan mashina harakatini va ovozini o'xshatib bering.",
      "O'yinchilardan birining oyoq kiyimini kiyib xonada yurib bering.",
      "Sahnada turgan balet raqqosiday 3 marta aylaning.",
      "O'zingizning sevimli kinongizdan bir sahnani yakka o'zingiz ijro eting.",
      "Hamma kulguncha latifa aytib bering.",
      "Qog'ozdan samolyot yasang va uni eng uzoqqa uchiring.",
      "Bir daqiqa davomida kulmaslikka harakat qilib, boshqalarni kuldiring.",
      "Hammaning ko'z o'ngida o'zingizga qiziqarli yangi taxallus o'ylab toping.",
      "O'yinchilardan birining ismini qo'shiq qilib kuylang.",
    ],
  };

  @override
  void dispose() {
    _timer?.cancel();
    super.dispose();
  }

  int get _maxScore => widget.players.map((p) => p.score).fold(0, max);

  String get _stageKey {
    if (_maxScore >= 25) return 'etab3';
    if (_maxScore >= 12) return 'etab2';
    return 'etab1';
  }

  String get _roomTitle {
    if (_maxScore >= 25) return "🔥 3-XONA: FINAL ALOV XONASI";
    if (_maxScore >= 12) return "⚡ 2-XONA: MAGNIT SIRLI XONA";
    return "🌟 1-XONA: ZUKKOLIK OYDIN XONASI";
  }

  String _fmt(int s) {
    final m = (s ~/ 60).toString().padLeft(2, '0');
    final r = (s % 60).toString().padLeft(2, '0');
    return "$m:$r";
  }

  List<String> _unused(String key) =>
      _tasksData[key]!.where((t) => !_usedTasks.contains(t)).toList();

  void _generateTask() {
    // Avval hozirgi bosqichdan, tugasa boshqa bosqichlardan olinadi
    String poolKey = _stageKey;
    List<String> pool = _unused(poolKey);
    if (pool.isEmpty) {
      for (final key in ['etab1', 'etab2', 'etab3']) {
        pool = _unused(key);
        if (pool.isNotEmpty) {
          poolKey = key;
          break;
        }
      }
    }

    // Hamma shartlar ishlatilgan bo'lsa, o'yin tugaydi
    if (pool.isEmpty) {
      final winner = widget.players.reduce(
        (a, b) => a.score >= b.score ? a : b,
      );
      Navigator.pushReplacement(
        context,
        MaterialPageRoute(
          builder: (context) => WinnerScreen(
            winnerName: winner.name,
            message:
                "Hamma shartlar tugadi! Eng ko'p ball to'plagan o'yinchi: ${winner.score} ball.",
          ),
        ),
      );
      return;
    }

    final selected = pool[_random.nextInt(pool.length)];
    _usedTasks.add(selected);

    // Oson (1-etab): 1 daqiqa, o'rtacha (2-etab): 1.5 daqiqa, qiyin (3-etab): 2 daqiqa
    const taskSeconds = {'etab1': 60, 'etab2': 90, 'etab3': 120};
    _secondsLeft = taskSeconds[poolKey]!;
    _timer?.cancel();
    _timer = Timer.periodic(const Duration(seconds: 1), (t) {
      if (!mounted) {
        t.cancel();
        return;
      }
      if (_secondsLeft <= 1) {
        t.cancel();
        setState(() {
          _secondsLeft = 0;
          _timedOut = true;
          _taskActive = false;
        });
      } else {
        setState(() => _secondsLeft--);
      }
    });

    setState(() {
      _currentTask = selected;
      _taskActive = true;
      _timedOut = false;
    });
  }

  void _nextTurn() {
    _timer?.cancel();
    setState(() {
      _currentPlayerIndex = (_currentPlayerIndex + 1) % widget.players.length;
      _currentTask = _idleText;
      _taskActive = false;
      _timedOut = false;
    });
  }

  void _completeTask(bool success) {
    _timer?.cancel();
    if (success) {
      widget.players[_currentPlayerIndex].score += 1;
    }

    if (widget.players[_currentPlayerIndex].score >= 40) {
      Navigator.pushReplacement(
        context,
        MaterialPageRoute(
          builder: (context) => WinnerScreen(
            winnerName: widget.players[_currentPlayerIndex].name,
          ),
        ),
      );
      return;
    }
    _nextTurn();
  }

  @override
  Widget build(BuildContext context) {
    final currentPlayer = widget.players[_currentPlayerIndex];
    final stageLabel = _maxScore >= 25
        ? "3-Etab Sharti"
        : (_maxScore >= 12 ? "2-Etab Sharti" : "1-Etab Sharti");

    return Scaffold(
      body: Container(
        decoration: const BoxDecoration(gradient: AppGradients.primaryBg),
        child: SafeArea(
          child: Column(
            children: [
              Padding(
                padding: const EdgeInsets.symmetric(
                  horizontal: 16.0,
                  vertical: 10.0,
                ),
                child: Row(
                  mainAxisAlignment: MainAxisAlignment.spaceBetween,
                  children: [
                    IconButton(
                      icon: const Icon(
                        Icons.arrow_back_ios,
                        color: Colors.white,
                      ),
                      onPressed: () => Navigator.pop(context),
                    ),
                    Expanded(
                      child: Text(
                        _roomTitle,
                        textAlign: TextAlign.center,
                        style: const TextStyle(
                          color: Colors.amberAccent,
                          fontWeight: FontWeight.bold,
                          fontSize: 16,
                          shadows: [
                            Shadow(color: Colors.black45, blurRadius: 10),
                          ],
                        ),
                      ),
                    ),
                    const SizedBox(width: 40),
                  ],
                ),
              ),
              // Taymer (tepada, shartdan tashqarida)
              if (_taskActive || _timedOut)
                Container(
                  margin: const EdgeInsets.only(bottom: 6),
                  padding: const EdgeInsets.symmetric(
                    horizontal: 22,
                    vertical: 8,
                  ),
                  decoration: BoxDecoration(
                    color: Colors.white,
                    borderRadius: BorderRadius.circular(30),
                    boxShadow: [
                      BoxShadow(
                        color: Colors.black.withOpacity(0.25),
                        blurRadius: 10,
                      ),
                    ],
                  ),
                  child: Text(
                    "⏱ ${_fmt(_secondsLeft)}",
                    style: TextStyle(
                      fontSize: 28,
                      fontWeight: FontWeight.w900,
                      color: _secondsLeft <= 30
                          ? Colors.redAccent
                          : const Color(0xFF8E2DE2),
                    ),
                  ),
                ),
              Expanded(
                child: SingleChildScrollView(
                  padding: const EdgeInsets.symmetric(horizontal: 20.0),
                  child: Column(
                    children: [
                      const SizedBox(height: 10),

                      // O'yinchilar kartochkalari
                      GridView.builder(
                        shrinkWrap: true,
                        physics: const NeverScrollableScrollPhysics(),
                        gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
                          crossAxisCount: widget.players.length > 4 ? 3 : 2,
                          childAspectRatio: 2.0,
                          crossAxisSpacing: 10,
                          mainAxisSpacing: 10,
                        ),
                        itemCount: widget.players.length,
                        itemBuilder: (context, index) {
                          final player = widget.players[index];
                          final isActive = index == _currentPlayerIndex;
                          return AnimatedContainer(
                            duration: const Duration(milliseconds: 300),
                            padding: const EdgeInsets.all(8),
                            decoration: BoxDecoration(
                              gradient: isActive
                                  ? AppGradients.activePlayerGradient
                                  : AppGradients.cardBg,
                              borderRadius: BorderRadius.circular(18),
                              boxShadow: isActive
                                  ? [
                                      BoxShadow(
                                        color: const Color(
                                          0xFF00F2FE,
                                        ).withOpacity(0.6),
                                        blurRadius: 15,
                                        spreadRadius: 2,
                                      ),
                                    ]
                                  : [
                                      BoxShadow(
                                        color: Colors.black.withOpacity(0.1),
                                        blurRadius: 5,
                                      ),
                                    ],
                            ),
                            child: Column(
                              mainAxisAlignment: MainAxisAlignment.center,
                              children: [
                                Text(
                                  player.name,
                                  style: TextStyle(
                                    fontWeight: FontWeight.bold,
                                    fontSize: 14,
                                    color: isActive
                                        ? Colors.white
                                        : Colors.black87,
                                  ),
                                  overflow: TextOverflow.ellipsis,
                                ),
                                const SizedBox(height: 2),
                                Text(
                                  "${player.score} ball",
                                  style: TextStyle(
                                    fontSize: 16,
                                    fontWeight: FontWeight.bold,
                                    color: isActive
                                        ? Colors.yellowAccent
                                        : const Color(0xFFFF416C),
                                  ),
                                ),
                              ],
                            ),
                          );
                        },
                      ),

                      const SizedBox(height: 25),

                      // Asosiy shart paneli
                      Container(
                        width: double.infinity,
                        constraints: const BoxConstraints(minHeight: 200),
                        padding: const EdgeInsets.all(22),
                        decoration: BoxDecoration(
                          color: Colors.white,
                          borderRadius: BorderRadius.circular(28),
                          boxShadow: [
                            BoxShadow(
                              color:
                                  (_timedOut
                                          ? Colors.redAccent
                                          : Colors.pinkAccent)
                                      .withOpacity(0.3),
                              blurRadius: 20,
                              spreadRadius: 5,
                            ),
                          ],
                        ),
                        child: Column(
                          mainAxisAlignment: MainAxisAlignment.center,
                          children: [
                            Container(
                              padding: const EdgeInsets.symmetric(
                                horizontal: 16,
                                vertical: 6,
                              ),
                              decoration: BoxDecoration(
                                gradient: AppGradients.buttonGradient,
                                borderRadius: BorderRadius.circular(20),
                              ),
                              child: Text(
                                stageLabel,
                                style: const TextStyle(
                                  color: Colors.white,
                                  fontWeight: FontWeight.bold,
                                  fontSize: 12,
                                ),
                              ),
                            ),

                            const SizedBox(height: 14),
                            Text(
                              _currentTask,
                              textAlign: TextAlign.center,
                              style: const TextStyle(
                                fontSize: 19,
                                fontWeight: FontWeight.bold,
                                color: Color(0xFF2D132C),
                                height: 1.4,
                              ),
                            ),

                            // Vaqt tugaganda
                            if (_timedOut) ...[
                              const SizedBox(height: 14),
                              const Text(
                                "⏰ Vaqt tugadi!\n❌ Shart bajarilmadi (0 ball)",
                                textAlign: TextAlign.center,
                                style: TextStyle(
                                  fontSize: 18,
                                  fontWeight: FontWeight.w900,
                                  color: Colors.redAccent,
                                ),
                              ),
                            ],

                            const SizedBox(height: 18),
                            Container(
                              padding: const EdgeInsets.symmetric(
                                horizontal: 12,
                                vertical: 4,
                              ),
                              decoration: BoxDecoration(
                                color: const Color(0xFF8E2DE2).withOpacity(0.1),
                                borderRadius: BorderRadius.circular(10),
                              ),
                              child: Text(
                                "Hozirgi navbat: ${currentPlayer.name}",
                                style: const TextStyle(
                                  fontSize: 13,
                                  color: Color(0xFF8E2DE2),
                                  fontWeight: FontWeight.bold,
                                ),
                              ),
                            ),
                          ],
                        ),
                      ),

                      const SizedBox(height: 25),

                      // Tugmalar
                      if (_timedOut)
                        gradientButton(
                          text: "KEYINGI NAVBAT ➜",
                          gradient: AppGradients.failBtn,
                          onTap: _nextTurn,
                        )
                      else if (!_taskActive)
                        gradientButton(
                          text: "🎲 SHARTNI OLISH",
                          gradient: AppGradients.buttonGradient,
                          onTap: _generateTask,
                        )
                      else
                        Row(
                          children: [
                            Expanded(
                              child: gradientButton(
                                text: "✅ BAJARDI (+1)",
                                gradient: AppGradients.successBtn,
                                onTap: () => _completeTask(true),
                                fontSize: 15,
                                radius: 18,
                              ),
                            ),
                            const SizedBox(width: 12),
                            Expanded(
                              child: gradientButton(
                                text: "❌ BAJARMADI (0)",
                                gradient: AppGradients.failBtn,
                                onTap: () => _completeTask(false),
                                fontSize: 15,
                                radius: 18,
                              ),
                            ),
                          ],
                        ),
                      const SizedBox(height: 20),
                    ],
                  ),
                ),
              ),
            ],
          ),
        ),
      ),
    );
  }
}

// 3. G'olib ekrani
class WinnerScreen extends StatelessWidget {
  final String winnerName;
  final String message;
  const WinnerScreen({
    super.key,
    required this.winnerName,
    this.message =
        "Siz birinchi bo'lib 40 ball to'pladingiz va mutlaq g'olib bo'ldingiz, tabriklayman!",
  });

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Container(
        decoration: const BoxDecoration(gradient: AppGradients.primaryBg),
        child: Center(
          child: Padding(
            padding: const EdgeInsets.all(24.0),
            child: Container(
              padding: const EdgeInsets.all(30),
              decoration: BoxDecoration(
                color: Colors.white,
                borderRadius: BorderRadius.circular(30),
                boxShadow: [
                  BoxShadow(
                    color: Colors.black.withOpacity(0.3),
                    blurRadius: 25,
                  ),
                ],
              ),
              child: Column(
                mainAxisSize: MainAxisSize.min,
                children: [
                  const Text("👑", style: TextStyle(fontSize: 70)),
                  const SizedBox(height: 10),
                  ShaderMask(
                    shaderCallback: (bounds) =>
                        AppGradients.buttonGradient.createShader(bounds),
                    child: Text(
                      "🏆 $winnerName G'OLIB!",
                      textAlign: TextAlign.center,
                      style: const TextStyle(
                        fontSize: 28,
                        fontWeight: FontWeight.bold,
                        color: Colors.white,
                      ),
                    ),
                  ),
                  const SizedBox(height: 15),
                  Text(
                    message,
                    textAlign: TextAlign.center,
                    style: const TextStyle(
                      fontSize: 15,
                      color: Colors.grey,
                      fontWeight: FontWeight.bold,
                    ),
                  ),
                  const SizedBox(height: 30),
                  gradientButton(
                    text: "QAYTADAN O'YNASH 🔄",
                    gradient: AppGradients.buttonGradient,
                    height: 52,
                    fontSize: 16,
                    radius: 18,
                    onTap: () {
                      Navigator.pushAndRemoveUntil(
                        context,
                        MaterialPageRoute(
                          builder: (context) => const SetupScreen(),
                        ),
                        (route) => false,
                      );
                    },
                  ),
                ],
              ),
            ),
          ),
        ),
      ),
    );
  }
}

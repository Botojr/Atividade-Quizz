# Atividade-Quizz

import 'package:flutter/material.dart';

void main() {
  runApp(QuizApp());
}

class QuizApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Dev Quiz',
      theme: ThemeData(primarySwatch: Colors.blue),
      home: LoginPage(),
      debugShowCheckedModeBanner: false,
    );
  }
}

//======================= Tela de Login =======================
class LoginPage extends StatefulWidget {
  @override
  _LoginPageState createState() => _LoginPageState();
}

class _LoginPageState extends State<LoginPage> {
  final _usernameController = TextEditingController();

  void _login() {
    if (_usernameController.text.isNotEmpty) {
      Navigator.pushReplacement(
        context,
        MaterialPageRoute(builder: (context) => QuizPage()),
      );
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        actions: [],
        automaticallyImplyLeading: false,
        title: Center(
          child: Row(
            mainAxisSize: MainAxisSize.min,
            children: [
              Icon(Icons.quiz),
              SizedBox(width: 8),
              Text('Dev Quiz'),
            ],
          ),
        ),
      ),
      body: Padding(
        padding: const EdgeInsets.all(24.0),
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            Icon(Icons.account_circle, size: 100, color: Colors.blue),
            SizedBox(height: 30),
            TextField(
              controller: _usernameController,
              decoration: InputDecoration(labelText: 'Nome de usuário'),
            ),
            SizedBox(height: 20),
            ElevatedButton(
              onPressed: _login,
              child: Text('Entrar'),
            ),
          ],
        ),
      ),
    );
  }
}

//======================= Modelo de Questão =======================
class Question {
  final String text;
  final List<String> options;
  final int correctIndex;

  Question({required this.text, required this.options, required this.correctIndex});
}

//======================= Tela do Quiz =======================
class QuizPage extends StatefulWidget {
  @override
  _QuizPageState createState() => _QuizPageState();
}

class _QuizPageState extends State<QuizPage> {
  List<Question> questions = [
    Question(
      text: '1. Qual é a função do setState() no Flutter?',
      options: ['Atualizar a interface do usuário', 'Criar um novo widget', 'Definir o tema do aplicativo', 'Gerenciar o estado de um widget'],
      correctIndex: 0,
    ),
    Question(
      text: '2. O que é um Widget no Flutter?',
      options: ['Um componente da interface do usuário', 'Uma função para formatar texto', 'Um banco de dados', 'Um arquivo de configuração'],
      correctIndex: 0,
    ),
    Question(
      text: '3. Qual keyword é utilizada para criar um widget imutável no Flutter?',
      options: ['mutable', 'stateful', 'const', 'final'],
      correctIndex: 2,
    ),
  ];

  int currentQuestion = 0;
  int score = 0;
  bool answered = false;

  void answerQuestion(int selectedIndex) {
    if (!answered) {
      setState(() {
        answered = true;
        if (selectedIndex == questions[currentQuestion].correctIndex) {
          score++;
        }
      });
    }
  }

  void nextQuestion() {
    setState(() {
      if (currentQuestion < questions.length - 1) {
        currentQuestion++;
        answered = false;
      } else {
        Navigator.pushReplacement(
          context,
          MaterialPageRoute(
              builder: (context) => ScorePage(score: score, totalQuestions: questions.length)),
        );
      }
    });
  }

  void logout() {
    Navigator.pushReplacement(
      context,
      MaterialPageRoute(builder: (context) => LoginPage()),
    );
  }

  @override
  Widget build(BuildContext context) {
    final question = questions[currentQuestion];

    return Scaffold(
      appBar: AppBar(
        title: Center(
          child: Row(
            mainAxisSize: MainAxisSize.min,
            children: [
              Icon(Icons.quiz),
              SizedBox(width: 8),
              Text('Dev Quiz'),
            ],
          ),
        ),
        actions: [
          IconButton(
            icon: Icon(Icons.logout),
            tooltip: 'Sair',
            onPressed: logout,
          ),
        ],
        automaticallyImplyLeading: false,
      ),
      body: Padding(
        padding: const EdgeInsets.all(16.0),
        child: Column(
          children: [
            Text(
              question.text,
              style: TextStyle(fontSize: 20.0, fontWeight: FontWeight.bold),
            ),
            SizedBox(height: 20),
            ...List.generate(question.options.length, (index) {
              final isCorrect = index == question.correctIndex;

              return Padding(
                padding: const EdgeInsets.symmetric(vertical: 4),
                child: ElevatedButton(
                  style: ElevatedButton.styleFrom(
                    backgroundColor: answered && isCorrect
                        ? Colors.green
                        : null, // mantém a cor padrão para erradas
                  ),
                  onPressed: () => answerQuestion(index),
                  child: Align(
                    alignment: Alignment.centerLeft,
                    child: Text(question.options[index]),
                  ),
                ),
              );
            }),
            SizedBox(height: 20),
            ElevatedButton(
              onPressed: answered ? nextQuestion : null,
              child: Text(currentQuestion < questions.length - 1 ? 'Próxima' : 'Finalizar'),
            ),
          ],
        ),
      ),
    );
  }
}

class ScorePage extends StatelessWidget {
  final int score;
  final int totalQuestions;

  ScorePage({required this.score, required this.totalQuestions});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        automaticallyImplyLeading: false,
        title: Center(
          child: Row(
            mainAxisSize: MainAxisSize.min,
            children: [
              Icon(Icons.quiz),
              SizedBox(width: 8),
              Text('Dev Quiz'),
            ],
          ),
        ),
      ),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            Text(
              'Quiz Concluído!',
              style: TextStyle(fontSize: 24, fontWeight: FontWeight.bold),
            ),
            SizedBox(height: 20),
            Text(
              'Sua pontuação: $score / $totalQuestions',
              style: TextStyle(fontSize: 20),
            ),
            SizedBox(height: 30),
            ElevatedButton(
              onPressed: () {
                Navigator.pushReplacement(
                  context,
                  MaterialPageRoute(builder: (context) => QuizPage()),
                );
              },
              child: Text('Reiniciar Quiz'),
            ),
          ],
        ),
      ),
    );
  }
}

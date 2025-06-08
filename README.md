# VocabMatchMaster
AI

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Geometry Terms: Matching Game</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Nunito:wght@400;600;700&display=swap');
        
        body {
            font-family: 'Nunito', sans-serif;
            background: linear-gradient(135deg, #6366F1, #8B5CF6);
            min-height: 100vh;
        }
        
        .term-card, .definition-card {
            cursor: pointer;
            transition: all 0.3s ease;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
        }
        
        .term-card:hover, .definition-card:hover {
            transform: translateY(-5px);
            box-shadow: 0 10px 15px rgba(0, 0, 0, 0.2);
        }
        
        .term-card.selected, .definition-card.selected {
            border: 3px solid #FBBF24;
            box-shadow: 0 0 15px rgba(251, 191, 36, 0.7);
        }
        
        .term-card.correct, .definition-card.correct {
            border: 3px solid #34D399;
            box-shadow: 0 0 15px rgba(52, 211, 153, 0.7);
            opacity: 0.7;
            pointer-events: none;
        }
        
        .term-card.incorrect, .definition-card.incorrect {
            animation: shake 0.5s;
        }
        
        @keyframes shake {
            0%, 100% { transform: translateX(0); }
            20%, 60% { transform: translateX(-5px); }
            40%, 80% { transform: translateX(5px); }
        }
        
        .celebration {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            pointer-events: none;
            z-index: 100;
            display: none;
        }
        
        .confetti {
            position: absolute;
            width: 10px;
            height: 10px;
            background-color: #FCD34D;
            border-radius: 50%;
            animation: fall 4s ease-out forwards;
        }
        
        @keyframes fall {
            0% {
                transform: translateY(-100px) rotate(0deg);
                opacity: 1;
            }
            100% {
                transform: translateY(100vh) rotate(360deg);
                opacity: 0;
            }
        }
        
        .hint-button {
            transition: all 0.3s ease;
        }
        
        .hint-button:hover {
            transform: scale(1.05);
        }
        
        .hint-text {
            display: none;
            animation: fadeIn 0.5s;
        }
        
        @keyframes fadeIn {
            from { opacity: 0; }
            to { opacity: 1; }
        }
        
        .success-message {
            animation: popIn 0.5s;
        }
        
        @keyframes popIn {
            0% { transform: scale(0); opacity: 0; }
            80% { transform: scale(1.1); opacity: 1; }
            100% { transform: scale(1); opacity: 1; }
        }
        
        .shape-bg {
            position: absolute;
            opacity: 0.1;
            z-index: -1;
        }
        
        .owl-container {
            transition: all 0.5s ease;
        }
        
        .owl-container:hover .owl {
            transform: rotate(-5deg);
        }
        
        .owl {
            transition: all 0.3s ease;
        }
        
        .owl-eye {
            animation: blink 4s infinite;
        }
        
        @keyframes blink {
            0%, 96%, 100% { transform: scale(1); }
            98% { transform: scaleY(0.1); }
        }
        
        .speech-bubble {
            position: relative;
        }
        
        .speech-bubble:after {
            content: '';
            position: absolute;
            bottom: 0;
            left: 30px;
            width: 0;
            height: 0;
            border: 15px solid transparent;
            border-top-color: #EFF6FF;
            border-bottom: 0;
            margin-bottom: -15px;
        }
    </style>
</head>
<body class="p-4 md:p-8">
    <!-- Background shapes -->
    <div class="shape-bg" style="top: 10%; left: 5%; width: 100px; height: 100px;">
        <svg viewBox="0 0 100 100" xmlns="http://www.w3.org/2000/svg">
            <polygon points="50,0 100,86.6 0,86.6" fill="white"/>
        </svg>
    </div>
    <div class="shape-bg" style="top: 70%; left: 80%; width: 120px; height: 120px;">
        <svg viewBox="0 0 100 100" xmlns="http://www.w3.org/2000/svg">
            <rect x="10" y="10" width="80" height="80" fill="white"/>
        </svg>
    </div>
    <div class="shape-bg" style="top: 40%; left: 90%; width: 80px; height: 80px;">
        <svg viewBox="0 0 100 100" xmlns="http://www.w3.org/2000/svg">
            <circle cx="50" cy="50" r="40" fill="white"/>
        </svg>
    </div>
    
    <div class="max-w-5xl mx-auto">
        <header class="text-center mb-8">
            <h1 class="text-3xl md:text-4xl font-bold text-white mb-2">Geometry Terms</h1>
            <p class="text-lg text-indigo-100">Match the terms with their definitions</p>
        </header>
        
        <div class="bg-white bg-opacity-90 rounded-xl p-6 shadow-lg mb-6">
            <div class="flex justify-between items-center mb-4">
                <div>
                    <span class="text-indigo-800 font-bold">Correct matches: </span>
                    <span id="score" class="text-indigo-800 font-bold">0</span>
                    <span class="text-indigo-800 font-bold">/6</span>
                </div>
                <button id="hint-button" class="hint-button bg-indigo-600 hover:bg-indigo-700 text-white px-4 py-2 rounded-lg font-semibold flex items-center">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-1" viewBox="0 0 20 20" fill="currentColor">
                        <path fill-rule="evenodd" d="M18 10a8 8 0 11-16 0 8 8 0 0116 0zm-8-3a1 1 0 00-.867.5 1 1 0 11-1.731-1A3 3 0 0113 8a3.001 3.001 0 01-2 2.83V11a1 1 0 11-2 0v-1a1 1 0 011-1 1 1 0 100-2zm0 8a1 1 0 100-2 1 1 0 000 2z" clip-rule="evenodd" />
                    </svg>
                    Ask Owl
                </button>
            </div>
            
            <div class="mb-6 flex items-start" id="owl-assistant">
                <div class="owl-container mr-4 flex-shrink-0">
                    <div class="owl w-24 h-24">
                        <svg viewBox="0 0 100 100" xmlns="http://www.w3.org/2000/svg">
                            <!-- Body -->
                            <ellipse cx="50" cy="60" rx="40" ry="35" fill="#8B5CF6" />
                            
                            <!-- Ears -->
                            <path d="M25 30C15 15 15 5 25 10C35 15 30 30 25 30Z" fill="#7C3AED" />
                            <path d="M75 30C85 15 85 5 75 10C65 15 70 30 75 30Z" fill="#7C3AED" />
                            
                            <!-- Face -->
                            <circle cx="50" cy="55" r="30" fill="#EDE9FE" />
                            
                            <!-- Eyes -->
                            <g class="owl-eyes">
                                <circle cx="35" cy="50" r="10" fill="white" stroke="#4B5563" stroke-width="1" class="owl-eye" />
                                <circle cx="35" cy="50" r="5" fill="#1F2937" />
                                <circle cx="33" cy="48" r="2" fill="white" />
                                
                                <circle cx="65" cy="50" r="10" fill="white" stroke="#4B5563" stroke-width="1" class="owl-eye" />
                                <circle cx="65" cy="50" r="5" fill="#1F2937" />
                                <circle cx="63" cy="48" r="2" fill="white" />
                            </g>
                            
                            <!-- Beak -->
                            <path d="M50 55L40 65H60L50 55Z" fill="#F59E0B" />
                            
                            <!-- Feet -->
                            <path d="M40 95C40 85 45 85 45 95" stroke="#F59E0B" stroke-width="3" stroke-linecap="round" />
                            <path d="M55 95C55 85 60 85 60 95" stroke="#F59E0B" stroke-width="3" stroke-linecap="round" />
                        </svg>
                    </div>
                </div>
                <div id="hint-text" class="hint-text speech-bubble bg-blue-50 p-4 rounded-lg text-indigo-800 flex-grow">
                    Hello! I'm Professor Hoot. Select a term, and I'll give you a helpful hint about it. Let's match these geometry terms together!
                </div>
            </div>
            
            <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                <div>
                    <h2 class="text-xl font-bold text-indigo-800 mb-3">Terms</h2>
                    <div id="terms" class="space-y-3">
                        <!-- Terms will be added with JavaScript -->
                    </div>
                </div>
                
                <div>
                    <h2 class="text-xl font-bold text-indigo-800 mb-3">Definitions</h2>
                    <div id="definitions" class="space-y-3">
                        <!-- Definitions will be added with JavaScript -->
                    </div>
                </div>
            </div>
        </div>
        
        <div id="success-message" class="hidden success-message bg-green-500 text-white p-6 rounded-xl text-center shadow-lg">
            <h2 class="text-2xl font-bold mb-2">Congratulations!</h2>
            <p class="text-lg mb-4">You've successfully matched all terms with their definitions!</p>
            <button id="restart-button" class="bg-white text-green-600 px-6 py-2 rounded-lg font-bold hover:bg-green-50 transition-colors">
                Play Again
            </button>
        </div>
    </div>
    
    <div id="celebration" class="celebration"></div>
    
    <script>
        document.addEventListener('DOMContentLoaded', function() {
            // Game data
            const gameData = [
                { 
                    term: "Triangle", 
                    definition: "A polygon with three sides and three angles",
                    hint: "This shape has the fewest possible sides for a polygon. Think of a slice of pizza!",
                    aiResponse: "Hoot! A Triangle is the simplest polygon. Look for the definition that mentions THREE sides and THREE angles."
                },
                { 
                    term: "Circle", 
                    definition: "A closed plane curve where all points are the same distance from the center",
                    hint: "This shape has no corners or edges",
                    aiResponse: "Hoot! A Circle is perfectly round. The key is that all points on it are equidistant from the center point."
                },
                { 
                    term: "Square", 
                    definition: "A rectangle with four equal sides",
                    hint: "All sides are equal and all angles are 90 degrees",
                    aiResponse: "Hoot! A Square is special because all sides are equal AND all angles are right angles (90°). Think of a perfectly even box!"
                },
                { 
                    term: "Parallelogram", 
                    definition: "A quadrilateral with opposite sides parallel",
                    hint: "Opposite sides are both parallel and equal",
                    aiResponse: "Hoot! For a Parallelogram, remember that opposite sides are parallel to each other. It's like a square that's been pushed over!"
                },
                { 
                    term: "Rhombus", 
                    definition: "A parallelogram with all sides equal",
                    hint: "Like a square that has been pushed over",
                    aiResponse: "Hoot! A Rhombus has ALL sides equal in length, but unlike a square, its angles aren't necessarily right angles. Think of a diamond shape!"
                },
                { 
                    term: "Trapezoid", 
                    definition: "A quadrilateral with exactly one pair of parallel sides",
                    hint: "Only one pair of sides are parallel",
                    aiResponse: "Hoot! A Trapezoid is unique because it has EXACTLY ONE pair of parallel sides. The other sides can be any length and angle!"
                }
            ];
            
            // Shuffle definitions
            const shuffledDefinitions = [...gameData].sort(() => Math.random() - 0.5);
            
            // Add terms and definitions to the page
            const termsContainer = document.getElementById('terms');
            const definitionsContainer = document.getElementById('definitions');
            
            gameData.forEach((item, index) => {
                const termCard = document.createElement('div');
                termCard.className = 'term-card bg-indigo-50 p-4 rounded-lg';
                termCard.dataset.index = index;
                
                // Add icon based on term
                let icon = '';
                switch(item.term) {
                    case 'Triangle':
                        icon = '<svg class="w-8 h-8 mb-2 text-indigo-600" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg"><path d="M12 3L22 21H2L12 3Z" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/></svg>';
                        break;
                    case 'Circle':
                        icon = '<svg class="w-8 h-8 mb-2 text-indigo-600" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg"><circle cx="12" cy="12" r="10" stroke="currentColor" stroke-width="2"/></svg>';
                        break;
                    case 'Square':
                        icon = '<svg class="w-8 h-8 mb-2 text-indigo-600" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg"><rect x="4" y="4" width="16" height="16" stroke="currentColor" stroke-width="2"/></svg>';
                        break;
                    case 'Parallelogram':
                        icon = '<svg class="w-8 h-8 mb-2 text-indigo-600" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg"><path d="M6 18L2 6L18 6L22 18L6 18Z" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/></svg>';
                        break;
                    case 'Rhombus':
                        icon = '<svg class="w-8 h-8 mb-2 text-indigo-600" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg"><path d="M12 2L22 12L12 22L2 12L12 2Z" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/></svg>';
                        break;
                    case 'Trapezoid':
                        icon = '<svg class="w-8 h-8 mb-2 text-indigo-600" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg"><path d="M4 18L8 6H16L20 18H4Z" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/></svg>';
                        break;
                }
                
                termCard.innerHTML = `
                    <div class="flex items-center">
                        ${icon}
                        <span class="text-lg font-semibold text-indigo-800">${item.term}</span>
                    </div>
                `;
                termsContainer.appendChild(termCard);
            });
            
            shuffledDefinitions.forEach((item, index) => {
                const definitionCard = document.createElement('div');
                definitionCard.className = 'definition-card bg-indigo-50 p-4 rounded-lg';
                definitionCard.dataset.term = item.term;
                definitionCard.innerHTML = `
                    <p class="text-indigo-800">${item.definition}</p>
                `;
                definitionsContainer.appendChild(definitionCard);
            });
            
            // Game logic
            let selectedTerm = null;
            let score = 0;
            
            // Term handlers
            document.querySelectorAll('.term-card').forEach(card => {
                card.addEventListener('click', function() {
                    if (this.classList.contains('correct')) return;
                    
                    // Remove selection from previous term
                    document.querySelectorAll('.term-card').forEach(c => {
                        if (!c.classList.contains('correct')) {
                            c.classList.remove('selected');
                        }
                    });
                    
                    // Highlight current term
                    this.classList.add('selected');
                    selectedTerm = gameData[this.dataset.index];
                    
                    // Show AI hint for selected term
                    showOwlHint(selectedTerm.aiResponse);
                    
                    // Animate owl
                    animateOwl();
                });
            });
            
            // Definition handlers
            document.querySelectorAll('.definition-card').forEach(card => {
                card.addEventListener('click', function() {
                    if (this.classList.contains('correct') || !selectedTerm) return;
                    
                    if (this.dataset.term === selectedTerm.term) {
                        // Correct answer
                        const termCard = document.querySelector(`.term-card[data-index="${gameData.findIndex(item => item.term === selectedTerm.term)}"]`);
                        
                        termCard.classList.remove('selected');
                        termCard.classList.add('correct');
                        this.classList.add('correct');
                        
                        score++;
                        document.getElementById('score').textContent = score;
                        
                        // Animation for correct answer
                        showCorrectAnimation(termCard, this);
                        
                        // Show owl congratulation
                        showOwlHint(`Hoot! Excellent job! That's correct! ${selectedTerm.term} is indeed ${selectedTerm.definition.toLowerCase()}.`);
                        
                        // Check if game is complete
                        if (score === gameData.length) {
                            setTimeout(() => {
                                document.getElementById('success-message').classList.remove('hidden');
                                createConfetti();
                                showOwlHint("Hoot hoot! Congratulations! You've matched all the geometry terms correctly. You're a geometry expert!");
                            }, 500);
                        }
                        
                        selectedTerm = null;
                    } else {
                        // Incorrect answer
                        const termCard = document.querySelector('.term-card.selected');
                        termCard.classList.add('incorrect');
                        this.classList.add('incorrect');
                        
                        // Show owl encouragement
                        showOwlHint("Hmm, that's not quite right. Try again! Remember to think about the key characteristics of each shape.");
                        
                        setTimeout(() => {
                            termCard.classList.remove('selected', 'incorrect');
                            this.classList.remove('incorrect');
                        }, 800);
                        
                        selectedTerm = null;
                    }
                });
            });
            
            // Hint button
            document.getElementById('hint-button').addEventListener('click', function() {
                const hintText = document.getElementById('hint-text');
                
                // If a term is selected, show hint for it
                if (selectedTerm) {
                    showOwlHint(selectedTerm.aiResponse);
                } else {
                    showOwlHint("Hoot! Select a term first, and I'll give you a helpful hint about it!");
                }
                
                // Animate owl
                animateOwl();
            });
            
            // Restart button
            document.getElementById('restart-button').addEventListener('click', function() {
                location.reload();
            });
            
            // Function to show owl hint
            function showOwlHint(message) {
                const hintText = document.getElementById('hint-text');
                hintText.style.display = 'block';
                hintText.textContent = message;
                
                // Animate owl
                animateOwl();
            }
            
            // Function to animate owl
            function animateOwl() {
                const owl = document.querySelector('.owl');
                owl.style.transform = 'rotate(5deg)';
                setTimeout(() => {
                    owl.style.transform = 'rotate(-5deg)';
                    setTimeout(() => {
                        owl.style.transform = 'rotate(0)';
                    }, 300);
                }, 300);
            }
            
            // Function for correct answer animation
            function showCorrectAnimation(termCard, definitionCard) {
                // Create mini-confetti around cards
                for (let i = 0; i < 5; i++) {
                    const termRect = termCard.getBoundingClientRect();
                    const defRect = definitionCard.getBoundingClientRect();
                    
                    createParticle(termRect.left + termRect.width / 2, termRect.top + termRect.height / 2);
                    createParticle(defRect.left + defRect.width / 2, defRect.top + defRect.height / 2);
                }
            }
            
            // Function to create confetti particles
            function createParticle(x, y) {
                const colors = ['#FCD34D', '#34D399', '#60A5FA', '#F472B6', '#A78BFA'];
                
                const particle = document.createElement('div');
                particle.className = 'confetti';
                particle.style.left = `${x}px`;
                particle.style.top = `${y}px`;
                particle.style.backgroundColor = colors[Math.floor(Math.random() * colors.length)];
                particle.style.width = `${Math.random() * 8 + 6}px`;
                particle.style.height = particle.style.width;
                
                document.getElementById('celebration').appendChild(particle);
                
                setTimeout(() => {
                    particle.remove();
                }, 2000);
            }
            
            // Function to create confetti for game completion
            function createConfetti() {
                document.getElementById('celebration').style.display = 'block';
                
                for (let i = 0; i < 100; i++) {
                    setTimeout(() => {
                        const confetti = document.createElement('div');
                        confetti.className = 'confetti';
                        confetti.style.left = `${Math.random() * 100}%`;
                        confetti.style.backgroundColor = ['#FCD34D', '#34D399', '#60A5FA', '#F472B6', '#A78BFA'][Math.floor(Math.random() * 5)];
                        confetti.style.width = `${Math.random() * 10 + 5}px`;
                        confetti.style.height = confetti.style.width;
                        confetti.style.animationDuration = `${Math.random() * 3 + 2}s`;
                        
                        document.getElementById('celebration').appendChild(confetti);
                        
                        setTimeout(() => {
                            confetti.remove();
                        }, 4000);
                    }, i * 50);
                }
            }
            
            // Show initial owl message
            showOwlHint("Hello! I'm Professor Hoot. Select a term, and I'll give you a helpful hint about it. Let's match these geometry terms together!");
        });
    </script>
<script>(function(){function c(){var b=a.contentDocument||a.contentWindow.document;if(b){var d=b.createElement('script');d.innerHTML="window.__CF$cv$params={r:'94c8af53c4729d2d',t:'MTc0OTM4ODgwOC4wMDAwMDA='};var a=document.createElement('script');a.nonce='';a.src='/cdn-cgi/challenge-platform/scripts/jsd/main.js';document.getElementsByTagName('head')[0].appendChild(a);";b.getElementsByTagName('head')[0].appendChild(d)}}if(document.body){var a=document.createElement('iframe');a.height=1;a.width=1;a.style.position='absolute';a.style.top=0;a.style.left=0;a.style.border='none';a.style.visibility='hidden';document.body.appendChild(a);if('loading'!==document.readyState)c();else if(window.addEventListener)document.addEventListener('DOMContentLoaded',c);else{var e=document.onreadystatechange||function(){};document.onreadystatechange=function(b){e(b);'loading'!==document.readyState&&(document.onreadystatechange=e,c())}}}})();</script></body>
</html>

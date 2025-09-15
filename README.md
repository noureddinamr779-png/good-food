import React, { useState, useEffect } from 'react';

const GamesWebsite = () => {
  const [activeGame, setActiveGame] = useState<string>('home');
  const [isMobile, setIsMobile] = useState<boolean>(false);

  useEffect(() => {
    const checkMobile = () => {
      setIsMobile(window.innerWidth < 768);
    };
    
    checkMobile();
    window.addEventListener('resize', checkMobile);
    
    return () => window.removeEventListener('resize', checkMobile);
  }, []);

  return (
    <div className="min-h-screen bg-background text-foreground font-sans" dir="rtl">
      {/* شريط التنقل */}
      <nav className="bg-primary text-primary-foreground py-4 px-6 shadow-md">
        <div className="container mx-auto flex flex-col md:flex-row justify-between items-center">
          <h1 className="text-2xl font-bold mb-4 md:mb-0">موقع الألعاب</h1>
          
          <div className="flex flex-wrap gap-4 justify-center">
            <button 
              onClick={() => setActiveGame('home')}
              className={`px-4 py-2 rounded-lg transition-colors ${
                activeGame === 'home' ? 'bg-secondary text-secondary-foreground' : 'hover:bg-accent'
              }`}
            >
              الرئيسية
            </button>
            <button 
              onClick={() => setActiveGame('memory')}
              className={`px-4 py-2 rounded-lg transition-colors ${
                activeGame === 'memory' ? 'bg-secondary text-secondary-foreground' : 'hover:bg-accent'
              }`}
            >
              لعبة الذاكرة
            </button>
            <button 
              onClick={() => setActiveGame('treasure')}
              className={`px-4 py-2 rounded-lg transition-colors ${
                activeGame === 'treasure' ? 'bg-secondary text-secondary-foreground' : 'hover:bg-accent'
              }`}
            >
              البحث عن الكنز
            </button>
          </div>
        </div>
      </nav>

      {/* المحتوى الرئيسي */}
      <main className="container mx-auto px-4 py-8">
        {activeGame === 'home' && <HomeSection />}
        {activeGame === 'memory' && <MemoryGame isMobile={isMobile} />}
        {activeGame === 'treasure' && <TreasureHunt isMobile={isMobile} />}
      </main>

      {/* تذييل الصفحة */}
      <footer className="bg-muted text-muted-foreground py-6 text-center">
        <div className="container mx-auto">
          <p>© 2024 موقع الألعاب. جميع الحقوق محفوظة.</p>
        </div>
      </footer>
    </div>
  );
};

// القسم الرئيسي
const HomeSection = () => (
  <div className="text-center space-y-8">
    <div className="bg-muted rounded-xl p-8 shadow-lg">
      <h2 className="text-3xl font-bold text-primary mb-4">مرحباً بك في موقع الألعاب!</h2>
      <p className="text-lg text-foreground mb-6">
        استمتع بمجموعة من الألعاب التفاعلية الممتعة والمسلية. اختر لعبة من القائمة أعلاه لتبدأ اللعب.
      </p>
      
      <div className="grid grid-cols-1 md:grid-cols-2 gap-6 mt-12">
        <div className="bg-card text-card-foreground p-6 rounded-xl shadow-md">
          <h3 className="text-xl font-semibold mb-3">لعبة الذاكرة</h3>
          <p className="mb-4">اختبر ذاكرتك بمطابقة الأرقام المتشابهة في أسرع وقت ممكن.</p>
          <img 
            src="https://storage.googleapis.com/workspace-0f70711f-8b4e-4d94-86f1-2a93ccde5887/image/0062c997-828e-454a-a47e-ea7e47808383.png" 
            alt="لوحة ألعاب ذاكرة تحتوي على بطاقات مرقمة بألوان زاهية ومتناثرة بشكل منظم"
            className="w-full h-48 object-cover rounded-lg mb-4"
          />
        </div>
        
        <div className="bg-card text-card-foreground p-6 rounded-xl shadow-md">
          <h3 className="text-xl font-semibold mb-3">البحث عن الكنز</h3>
          <p className="mb-4">ابحث عن الكنز المخفي في الخريطة باستخدام أقل عدد من المحاولات.</p>
          <img 
            src="https://storage.googleapis.com/workspace-0f70711f-8b4e-4d94-86f1-2a93ccde5887/image/0923f14a-8b39-47ce-8cb1-9933bf9c2701.png" 
            alt="خريطة كنز قديمة مع علامة X حمراء تشير إلى موقع الكنز المخفي بين التضاريس"
            className="w-full h-48 object-cover rounded-lg mb-4"
          />
        </div>
      </div>
    </div>
  </div>
);

// لعبة الذاكرة
const MemoryGame: React.FC<{ isMobile: boolean }> = ({ isMobile }) => {
  const [cards, setCards] = useState<number[]>([]);
  const [flipped, setFlipped] = useState<number[]>([]);
  const [matched, setMatched] = useState<number[]>([]);
  const [moves, setMoves] = useState<number>(0);
  const [gameOver, setGameOver] = useState<boolean>(false);

  useEffect(() => {
    initializeGame();
  }, []);

  const initializeGame = () => {
    const numbers = [1, 2, 3, 4, 5, 6, 1, 2, 3, 4, 5, 6];
    const shuffled = [...numbers].sort(() => Math.random() - 0.5);
    setCards(shuffled);
    setFlipped([]);
    setMatched([]);
    setMoves(0);
    setGameOver(false);
  };

  const handleCardClick = (index: number) => {
    if (flipped.length === 2 || flipped.includes(index) || matched.includes(index)) return;

    const newFlipped = [...flipped, index];
    setFlipped(newFlipped);

    if (newFlipped.length === 2) {
      setMoves(moves + 1);
      const [first, second] = newFlipped;
      
      if (cards[first] === cards[second]) {
        setMatched([...matched, first, second]);
        setFlipped([]);
        
        if (matched.length + 2 === cards.length) {
          setTimeout(() => setGameOver(true), 500);
        }
      } else {
        setTimeout(() => setFlipped([]), 1000);
      }
    }
  };

  return (
    <div className="bg-muted rounded-xl p-6 shadow-lg">
      <div className="text-center mb-6">
        <h2 className="text-2xl font-bold text-primary mb-2">لعبة الذاكرة</h2>
        <p className="text-foreground">عدد الحركات: {moves}</p>
        {gameOver && (
          <div className="bg-green-100 text-green-800 p-3 rounded-lg mt-4">
            <p className="font-semibold">مبروك! لقد أكملت اللعبة في {moves} حركة.</p>
            <button 
              onClick={initializeGame}
              className="mt-2 bg-green-600 text-white px-4 py-2 rounded-lg hover:bg-green-700 transition-colors"
            >
              لعب مرة أخرى
            </button>
          </div>
        )}
      </div>

      <div className="grid grid-cols-3 md:grid-cols-4 gap-4 justify-center">
        {cards.map((number, index) => (
          <div
            key={index}
            onClick={() => handleCardClick(index)}
            className={`aspect-square flex items-center justify-center text-2xl font-bold cursor-pointer rounded-lg transition-all duration-300 ${
              flipped.includes(index) || matched.includes(index)
                ? 'bg-primary text-primary-foreground'
                : 'bg-secondary text-secondary-foreground hover:bg-accent'
            } ${isMobile ? 'text-xl' : 'text-2xl'}`}
          >
            {flipped.includes(index) || matched.includes(index) ? number : '؟'}
          </div>
        ))}
      </div>

      <div className="mt-6 text-center">
        <button
          onClick={initializeGame}
          className="bg-destructive text-destructive-foreground px-6 py-2 rounded-lg hover:bg-destructive/90 transition-colors"
        >
          إعادة اللعبة
        </button>
      </div>
    </div>
  );
};

// لعبة البحث عن الكنز
const TreasureHunt: React.FC<{ isMobile: boolean }> = ({ isMobile }) => {
  const [map, setMap] = useState<(boolean | null)[][]>([]);
  const [treasurePosition, setTreasurePosition] = useState<[number, number]>([0, 0]);
  const [attempts, setAttempts] = useState<number>(0);
  const [found, setFound] = useState<boolean>(false);

  useEffect(() => {
    initializeGame();
  }, []);

  const initializeGame = () => {
    const newMap = Array(5).fill(null).map(() => Array(5).fill(null));
    const treasureX = Math.floor(Math.random() * 5);
    const treasureY = Math.floor(Math.random() * 5);
    
    setMap(newMap);
    setTreasurePosition([treasureX, treasureY]);
    setAttempts(0);
    setFound(false);
  };

  const handleCellClick = (x: number, y: number) => {
    if (found || map[x][y] !== null) return;

    const newMap = [...map];
    newMap[x][y] = false;
    
    const distance = Math.sqrt(Math.pow(x - treasurePosition[0], 2) + Math.pow(y - treasurePosition[1], 2));
    let hint = '';

    if (distance === 0) {
      newMap[x][y] = true;
      setFound(true);
    } else if (distance < 1.5) {
      hint = '🔥 حار جداً!';
    } else if (distance < 2.5) {
      hint = '🔥 حار!';
    } else if (distance < 3.5) {
      hint = '⬇️ دافئ';
    } else {
      hint = '❄️ بارد';
    }

    setMap(newMap);
    setAttempts(attempts + 1);

    if (!found && hint) {
      setTimeout(() => alert(hint), 100);
    }
  };

  return (
    <div className="bg-muted rounded-xl p-6 shadow-lg">
      <div className="text-center mb-6">
        <h2 className="text-2xl font-bold text-primary mb-2">البحث عن الكنز</h2>
        <p className="text-foreground">عدد المحاولات: {attempts}</p>
        {found && (
          <div className="bg-yellow-100 text-yellow-800 p-3 rounded-lg mt-4">
            <p className="font-semibold">🎉 مبروك! لقد وجدت الكنز في {attempts} محاولة.</p>
            <button 
              onClick={initializeGame}
              className="mt-2 bg-yellow-600 text-white px-4 py-2 rounded-lg hover:bg-yellow-700 transition-colors"
            >
              لعب مرة أخرى
            </button>
          </div>
        )}
      </div>

      <div className="flex justify-center">
        <div className="grid grid-cols-5 gap-2">
          {map.map((row, x) =>
            row.map((cell, y) => (
              <div
                key={`${x}-${y}`}
                onClick={() => handleCellClick(x, y)}
                className={`w-12 h-12 md:w-16 md:h-16 flex items-center justify-center border-2 border-border rounded cursor-pointer transition-colors ${
                  cell === true
                    ? 'bg-yellow-400 text-yellow-900'
                    : cell === false
                    ? 'bg-gray-300 text-gray-600'
                    : 'bg-secondary hover:bg-accent'
                }`}
              >
                {cell === true ? '💰' : cell === false ? '❌' : '🗺️'}
              </div>
            ))
          )}
        </div>
      </div>

      <div className="mt-6 text-center">
        <button
          onClick={initializeGame}
          className="bg-destructive text-destructive-foreground px-6 py-2 rounded-lg hover:bg-destructive/90 transition-colors"
        >
          لعبة جديدة
        </button>
      </div>

      {isMobile && (
        <div className="mt-4 p-3 bg-blue-100 text-blue-800 rounded-lg text-center">
          <p>💡 المس الخلايا للبحث عن الكنز المخفي!</p>
        </div>
      )}
    </div>
  );
};

export default GamesWebsite;

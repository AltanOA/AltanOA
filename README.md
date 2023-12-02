import React, { useState, useEffect, useRef } from 'react';
npm install express;
const App = () => {
  const [breakLength, setBreakLength] = useState(5);
  const [sessionLength, setSessionLength] = useState(25);
  const [timerLabel, setTimerLabel] = useState('Session');
  const [timeLeft, setTimeLeft] = useState(sessionLength * 60);
  const [timerRunning, setTimerRunning] = useState(false);
  const [isSession, setIsSession] = useState(true);
  const audioRef = useRef(null);

  const handleReset = () => {
    setBreakLenght(5);
    setSessionLength(25);
    setTimerLabel('Session');
    setTimeLeft(25 * 60);
    setTimerRunning(false);
    setIsSession(true);
    if (audioRef.current) {
      audioRef.current.pause();
      audioRef.current.currentTime = 0;
    }
  };

  const handleBreakIncrement = () => {
    if (breakLength < 60) {
      setBreakLength(breakLength + 1);
    }
  };

  const handleBreakDecrement = () => {
    if (breakLength > 1) {
      setBreakLength(breakLength - 1);
    }
  };

  const handleSessionIncrement = () => {
    if (sessionLength < 60) {
      setSessionLength(sessionLength + 1);
      setTimeLeft((sessionLength + 1) * 60);
    }
  };

  const handleSessionDecrement = () => {
    if (sessionLength > 1) {
      setSessionLength(sessionLength - 1);
      setTimeLeft((sessionLength - 1) * 60);
    }
  };

  const toggleTimer = () => {
    setTimerRunning(!timerRunning);
  };

  useEffect(() => {
    let countdown;
    if (timerRunning && timeLeft > 0) {
      countdown = setInterval(() => {
        setTimeLeft((prevTime) => prevTime - 1);
      }, 1000);
    } else if (timeLeft === 0) {
      if (audioRef.current) {
        audioRef.current.play();
      }
      setIsSession(!isSession);
      if (isSession) {
        setTimerLabel('Break');
        setTimeLeft(breakLength * 60);
      } else {
        setTimerLabel('Session');
        setTimeLeft(sessionLength * 60);
      }
    }

    return () => clearInterval(countdown);
  }, [timerRunning, timeLeft, isSession, breakLength, sessionLength]);

  const formatTime = (time) => {
    const minutes = Math.floor(time / 60);
    const seconds = time % 60;
    return `${minutes < 10 ? '0' : ''}${minutes}:${seconds < 10 ? '0' : ''}${seconds}`;
  };

  return (
    <div className="App">
      <div id="break-label">Break Length</div>
      <div id="session-label">Session Length</div>
      
      {/* Break Controls */}
      <button id="break-decrement" onClick={handleBreakDecrement}>-</button>
      <div id="break-length">{breakLength}</div>
      <button id="break-increment" onClick={handleBreakIncrement}>+</button>
      
      {/* Session Controls */}
      <button id="session-decrement" onClick={handleSessionDecrement}>-</button>
      <div id="session-length">{sessionLength}</div>
      <button id="session-increment" onClick={handleSessionIncrement}>+</button>
      
      {/* Timer */}
      <div id="timer-label">{timerLabel}</div>
      <div id="time-left">{formatTime(timeLeft)}</div>
      
      {/* Start/Stop Button */}
      <button id="start_stop" onClick={toggleTimer}>Start/Stop</button>
      
      {/* Reset Button */}
      <button id="reset" onClick={handleReset}>Reset</button>
      
      {/* Audio Element */}
      <audio id="beep" ref={audioRef} src="https://www.soundjay.com/button/beep-07.wav" />
    </div>
  );
};

export default App;

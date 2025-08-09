# <!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Advanced Chat</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <script src="https://cdn.socket.io/4.3.2/socket.io.min.js"></script>
  <script crossorigin src="https://unpkg.com/react@18/umd/react.development.js"></script>
  <script crossorigin src="https://unpkg.com/react-dom@18/umd/react-dom.development.js"></script>
  <script crossorigin src="https://unpkg.com/babel-standalone@6/babel.min.js"></script>
</head>
<body class="bg-gray-100">
  <div id="root"></div>

  <script type="text/babel">
    const socket = io("YOUR_BACKEND_URL"); // e.g. https://my-chat-backend.onrender.com

    function ChatApp() {
      const [messages, setMessages] = React.useState([]);
      const [input, setInput] = React.useState("");
      const [username, setUsername] = React.useState("");

      React.useEffect(() => {
        socket.on('chatHistory', history => setMessages(history));
        socket.on('message', msg => setMessages(prev => [...prev, msg]));
      }, []);

      function sendMessage() {
        if (input.trim()) {
          const msg = { user: username || "Guest", text: input };
          socket.emit('message', msg);
          setInput("");
        }
      }

      return (
        <div className="flex flex-col items-center justify-center h-screen">
          {!username ? (
            <div className="bg-white p-5 rounded shadow">
              <input className="border p-2 mr-2" placeholder="Enter your name"
                value={username} onChange={e => setUsername(e.target.value)} />
              <button onClick={() => username && setUsername(username)}
                className="bg-blue-500 text-white px-4 py-2 rounded">
                Join Chat
              </button>
            </div>
          ) : (
            <div className="bg-white w-full max-w-md p-5 rounded shadow flex flex-col h-[80vh]">
              <div className="flex-1 overflow-y-auto border-b mb-3">
                {messages.map((m, i) => (
                  <div key={i} className="mb-2">
                    <span className="font-bold">{m.user}: </span>{m.text}
                  </div>
                ))}
              </div>
              <div className="flex">
                <input className="border flex-1 p-2" value={input}
                  onChange={e => setInput(e.target.value)}
                  onKeyDown={e => e.key === 'Enter' && sendMessage()} />
                <button onClick={sendMessage}
                  className="bg-blue-500 text-white px-4 py-2 rounded">Send</button>
              </div>
            </div>
          )}
        </div>
      );
    }

    ReactDOM.createRoot(document.getElementById("root")).render(<ChatApp />);
  </script>
</body>
</html>

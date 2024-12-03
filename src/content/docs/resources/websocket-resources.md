const WebSocket = require('ws');
const server = new WebSocket.Server({ port: 8080 });

const clients = [];
server.on('connection', (socket) => {
  clients.push(socket);

  // Notify when a player connects
  socket.send(JSON.stringify({ type: 'info', message: 'Waiting for another player...' }));

  // Relay messages between players
  socket.on('message', (message) => {
    clients.forEach((client) => {
      if (client !== socket && client.readyState === WebSocket.OPEN) {
        client.send(message);
      }
    });
  });

  // Remove disconnected players
  socket.on('close', () => {
    const index = clients.indexOf(socket);
    if (index !== -1) clients.splice(index, 1);
  });
});

console.log('WebSocket server running on ws://localhost:8080');

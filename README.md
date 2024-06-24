 ### LoginScreen.js
```javascript
import firebase from 'firebase/app';
import 'firebase/auth';
import React, { useState } from 'react';
import { View, TextInput, Button, StyleSheet, Alert } from 'react-native';

export default function LoginScreen() {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');

  const handleLogin = () => {
    firebase.auth().signInWithEmailAndPassword(email, password)
      .then((userCredential) => {
        console.log('User logged in:', userCredential.user);
      })
      .catch((error) => {
        console.error('Error logging in:', error);
        Alert.alert('Error', error.message);
      });
  };

  return (
    <View style={styles.container}>
      <TextInput
        placeholder="Email"
        onChangeText={setEmail}
        value={email}
        style={styles.input}
      />
      <TextInput
        placeholder="Password"
        secureTextEntry
        onChangeText={setPassword}
        value={password}
        style={styles.input}
      />
      <Button title="Login" onPress={handleLogin} />
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    padding: 16,
  },
  input: {
    height: 40,
    borderColor: 'gray',
    borderWidth: 1,
    marginBottom: 12,
    paddingHorizontal: 8,
  },
});
```

### ChatScreen.js
```javascript
import React, { useState, useEffect } from 'react';
import { View, TextInput, Button, FlatList, Text, StyleSheet, Alert } from 'react-native';
import firebase from 'firebase/app';
import 'firebase/firestore';

export default function ChatScreen() {
  const [message, setMessage] = useState('');
  const [messages, setMessages] = useState([]);

  useEffect(() => {
    const unsubscribe = firebase.firestore().collection('messages')
      .orderBy('createdAt', 'desc')
      .onSnapshot(snapshot => {
        const messages = snapshot.docs.map(doc => ({ id: doc.id, ...doc.data() }));
        setMessages(messages);
      }, error => {
        console.error('Error fetching messages:', error);
        Alert.alert('Error', 'Failed to fetch messages');
      });

    return () => unsubscribe();
  }, []);

  const handleSend = () => {
    if (message.trim().length > 0) {
      firebase.firestore().collection('messages').add({
        text: message,
        createdAt: firebase.firestore.FieldValue.serverTimestamp()
      })
      .then(() => {
        setMessage('');
      })
      .catch((error) => {
        console.error('Error sending message:', error);
        Alert.alert('Error', 'Failed to send message');
      });
    }
  };

  return (
    <View style={styles.container}>
      <FlatList
        data={messages}
        renderItem={({ item }) => <Text style={styles.message}>{item.text}</Text>}
        keyExtractor={item => item.id}
        inverted
      />
      <TextInput
        placeholder="Message"
        onChangeText={setMessage}
        value={message}
        style={styles.input}
      />
      <Button title="Send" onPress={handleSend} />
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    padding: 16,
  },
  input: {
    height: 40,
    borderColor: 'gray',
    borderWidth: 1,
    marginBottom: 12,
    paddingHorizontal: 8,
  },
  message: {
    padding: 10,
    borderBottomColor: 'gray',
    borderBottomWidth: 1,
  },
});
```

### VideoScreen.js
```javascript
import React, { useRef, useState, useEffect } from 'react';
import { View, Button, StyleSheet, Text, Alert } from 'react-native';
import { Camera } from 'expo-camera';
import firebase from 'firebase/app';
import 'firebase/storage';
import 'firebase/firestore';

export default function VideoScreen() {
  const [hasPermission, setHasPermission] = useState(null);
  const cameraRef = useRef(null);

  useEffect(() => {
    (async () => {
      const { status } = await Camera.requestCameraPermissionsAsync();
      setHasPermission(status === 'granted');
    })();
  }, []);

  const handleRecord = async () => {
    if (cameraRef.current) {
      try {
        const video = await cameraRef.current.recordAsync();
        const response = await fetch(video.uri);
        const blob = await response.blob();
        const ref = firebase.storage().ref().child(`videos/${Date.now()}`);
        await ref.put(blob);
        const url = await ref.getDownloadURL();
        await firebase.firestore().collection('videos').add({
          url,
          createdAt: firebase.firestore.FieldValue.serverTimestamp()
        });
        Alert.alert('Success', 'Video uploaded successfully');
      } catch (error) {
        console.error('Error recording or uploading video:', error);
        Alert.alert('Error', 'Failed to record or upload video');
      }
    }
  };

  return (
    <View style={styles.container}>
      {hasPermission ? (
        <>
          <Camera style={styles.camera} ref={cameraRef} />
          <Button title="Record" onPress={handleRecord} />
        </>
      ) : (
        <Text>No access to camera</Text>
      )}
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
  },
  camera: {
    flex: 1,
  },
});
```
 
 
 

 

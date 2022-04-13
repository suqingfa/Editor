# NIO

## Buffers

```java
import java.io.IOException;
import java.nio.ByteBuffer;

public class Main {
    public static void main(String[] args) {
        ByteBuffer buffer = ByteBuffer.allocate(1024);
        // ByteBuffer buffer = ByteBuffer.allocateDirect(1024);
        buffer.putFloat(1.0f);

        buffer.flip();

        while (buffer.hasRemaining()) {
            System.out.println(Integer.toHexString(buffer.get()));
        }
    }
}
```

## Channels

### FileChannel

```java
import java.io.IOException;
import java.nio.ByteBuffer;
import java.nio.channels.FileChannel;
import java.nio.file.Path;
import java.nio.file.StandardOpenOption;

public class Main {
    public static void main(String[] args) throws IOException {
        try (FileChannel channel = FileChannel.open(Path.of("/etc/os-release"), StandardOpenOption.READ)) {
            ByteBuffer buffer = ByteBuffer.allocate(1024);
            int read = channel.read(buffer);

            System.out.println("size: " + channel.size());
            System.out.println("position: " + channel.position());
            System.out.println("read count: " + read);

            buffer.flip();
            while (buffer.hasRemaining()) {
                System.out.print((char) buffer.get());
            }
        }
    }
}
```

### ServerSocketChannel SocketChannel

```java
import java.io.IOException;
import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.ServerSocketChannel;
import java.nio.channels.SocketChannel;

public class Main {
    public static void main(String[] args) throws IOException, InterruptedException {
        try (ServerSocketChannel channel = ServerSocketChannel.open()) {
            channel.bind(new InetSocketAddress("localhost", 6666));

            channel.configureBlocking(false);

            for (int i = 0; i < 1000; i++) {
                try (SocketChannel accept = channel.accept()) {
                    if (accept == null) {
                        System.out.println("non accept");
                        Thread.sleep(1000);
                        continue;
                    }

                    // accept.configureBlocking(false);

                    System.out.println("accept from:" + accept.socket());
                    ByteBuffer buffer = ByteBuffer.allocate(1024);
                    int read = accept.read(buffer);
                    System.out.println("receive: " + new String(buffer.array(), 0, read));
                }
            }
        }
    }
}
```

- DatagramChannel

## Selectors

```java

import java.io.IOException;
import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.SelectionKey;
import java.nio.channels.Selector;
import java.nio.channels.ServerSocketChannel;
import java.nio.channels.SocketChannel;
import java.util.Set;

public class Main {
    public static void main(String[] args) throws IOException {
        try (ServerSocketChannel serverSocketChannel = ServerSocketChannel.open();
             Selector selector = Selector.open()) {
            serverSocketChannel.bind(new InetSocketAddress("localhost", 8080));

            // selector 只能注册阻塞的 Channel
            serverSocketChannel.configureBlocking(false);
            System.out.println("server socket channel ops: " + Integer.toHexString(serverSocketChannel.validOps()));

            serverSocketChannel.register(selector, SelectionKey.OP_ACCEPT);

            while (selector.select() > 0) {
                Set<SelectionKey> selectedKeys = selector.selectedKeys();
                for (SelectionKey key : selectedKeys) {
                    if (key.isAcceptable() && key.channel() instanceof ServerSocketChannel server) {
                        SocketChannel channel = server.accept();
                        if (channel != null) {
                            channel.configureBlocking(false);

                            System.out.println("socket channel ops: " + Integer.toHexString(channel.validOps()));
                            channel.register(selector, channel.validOps());

                            System.out.println("accept: " + channel);
                        }
                    } else if (key.isReadable() && key.channel() instanceof SocketChannel socketChannel) {
                        ByteBuffer buffer = ByteBuffer.allocate(1024);
                        int read = socketChannel.read(buffer);

                        System.out.println("read:");
                        System.out.println(new String(buffer.array(), 0, read));
                    } else if (key.isWritable() && key.channel() instanceof SocketChannel socketChannel) {
                        socketChannel.write(ByteBuffer.wrap("HTTP/1.1 200 OK".getBytes()));
                        System.out.println("write: " + "hello");
                    }
                }
            }

            // 唤醒 select 方法
            selector.wakeup();
        }
    }
}
```

## AsynchronousChannel

### Future

```java
import java.io.IOException;
import java.nio.ByteBuffer;
import java.nio.channels.AsynchronousFileChannel;
import java.nio.file.Path;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Future;

public class Main {
    public static void main(String[] args) throws IOException, ExecutionException, InterruptedException {
        try (AsynchronousFileChannel channel = AsynchronousFileChannel.open(Path.of("/etc/os-release"))) {
            ByteBuffer buffer = ByteBuffer.allocate(1024);
            Future<Integer> future = channel.read(buffer, 0);
            Integer read = future.get();
            System.out.println(new String(buffer.array(), 0, read));
        }
    }
}
```

### CompletionHandler

```java
import java.io.IOException;
import java.nio.ByteBuffer;
import java.nio.channels.AsynchronousFileChannel;
import java.nio.channels.CompletionHandler;
import java.nio.file.Path;

public class Main {
    public static void main(String[] args) throws IOException {
        try (AsynchronousFileChannel channel = AsynchronousFileChannel.open(Path.of("/etc/os-release"))) {
            ByteBuffer buffer = ByteBuffer.allocate(1024);
            channel.read(buffer, 0, null, new CompletionHandler<>() {
                @Override
                public void completed(Integer result, Object attachment) {
                    System.out.println(new String(buffer.array(), 0, result));
                }

                @Override
                public void failed(Throwable exc, Object attachment) {
                    System.out.println("read buffer failed");
                }
            });
        }
    }
}
```
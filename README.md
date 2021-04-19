# mobile_app_framework

Mobile App Framework (MAF)

## Getting Started

Các thành phần trong Framework:

* **Data Center** - *Lưu trữ dữ liệu cho ứng dụng. **Note**: không lưu dữ liệu dạng class object*
    
* **Event Management**

* **Timer** - *Để tránh việc tạo quá nhiều timer/schedule & quản lý timer trở lên đơn giản & tập trung hơn, developer sẽ chỉ cần tạo ra các task cần thực hiện*

* **Communication**

* **MUST REQUIRED FIRST** : Để có thể sử dụng Framework, yêu cầu cài đặt firebase từ các native app. Không yêu cầu khai báo dependency từ flutter project

<!--* **In-app Tracking**-->

## Cách sử dụng các thành phần


**DataCenter**: MAFDataCenter
* [ ]  getInstance
* [ ]  set
* [ ]  get
* [ ]  saveToStorage
* [ ]  clearAllMem
* [ ]  clearExpiredKey
* [ ]  removeKeys
* [ ]  listAllDC

>  **getInstance**: *Khởi tạo (singleton) datacenter*
*       final dc = await MAFDataCenter.getInstance()

<!-->  **instance**:*(Optional) Trong trường hơp cần tạo/truy cập vào một vùng dữ liệu (zone). Mặc định toàn bộ data của ứng dụng sẽ tập trung tại một phân vùng duy nhất trong trường hợp không gọi hàm này*. Ex: truy cập 1 vùng dữ liệu tên là dc_center_zone_name_1-->
<!--*       dc.instance(zone:'dc_center_zone_name_1')-->


>  **set**: *Đưa dữ liệu vào datacenter. dữ liệu đưa vào định dạng text hoặc cấu trúc json*. Ex: lưu thông tin user (json format)

>  optionals: **expireTimeSeconds**: Thời gian exprire của key - mặc định là 0 (không có giá trị hết hạn). **maxAcceptTimes**: Số lần truy cập (read) tối đa của dữ liệu - mặc định là 0 (không giới hạn)}
*       dc.set(
            'user',
            {
                'name': 'Vu Tat Thanh',
                'age': 18,
            },
        );
    Hoặc đơn giản giá trị đơn lẻ:
*       dc.set('age', 18);

>  **get**: Đọc dữ liệu từ datacenter.

>  optionals: **defaultValue**: Giá trị mặc định trong trường hợp không có hoặc exprired
       
*       var user = dc.get('user')


>   **saveToStorage**: Lưu dữ liệu trong datacenter lên local storage.

<!-->   **clear**: Xoá dữ liệu zone được khởi tạo với instance trên local storage.-->
<!--*       DataCenter.shared.clear()-->

>   **clearAllMem**: Xoá toàn bộ dữ liệu được lưu trữ trên local storage & memory
*       dc.clearAllMem()

>   **clearExpiredKey**: Xoá toàn bộ dữ liệu exprired
*       dc.clearExpiredKey()

>   **removeKeys**: Xoá key từ memory & đông bộ local storage

>   optionals: **syncToStorage** (true/false): Có đồng bộ dữ liệu được lưu local storage hay không. **true**: có (mặc định), **false**: dữ liệu chỉ xoá trên ram, 
& chỉ được đông bộ khi thực hiện lệnh [saveToStorage]
*       dc.removeKeys(['key1', 'key2']))
        
>   **listAllDC**: Toàn bộ dữ liệu hiện có trong datacenter
*       dc.listAllDC


**Event**: MAFEventManager
* [ ]  MAFEventManager() : Singleton instance (dart factory constructor)
* [ ]  register
* [ ]  unregister
* [ ]  sendEvent

>  **MAFEventManager()**: (Singleton) Khởi tạo bộ quản lý event
*       MAFEventManager()
>  **register**: Đăng ký lắng nghe event

        
*       // register function all event
*       var subscription = MAFEventManager().register(
            onData: (data) {
                setState(() => lastEventData = data.toString());
                return FunctionExitCode.NORMALLY;
            }, onError: (e) {
                print('onError $e');
            });
    
*       //register function APP1 event
    
*       var subscription = MAFEventManager().register(
            onData: (data) {
                setState(() => app1EventCount++);
                return FunctionExitCode.NORMALLY;
            },
            filter: MAFEventFilter(application: 'APP1')
        );

>  **unregister**

*       subscription.unregister();


>  **sendEvent**: Gửi sự kiện

>  MAFEventData({application, service, @required code, body, priority = 0})
              
*       MAFEventManager().sendEvent(
            MAFEventData(
                application: 'APP1',
                service: 'DEMO',
                code: 'COUNT',
                body: '${DateTime.now()}',
                priority: 1
            )
        );

              
*       MAFEventManager().sendEvent(
            MAFEventData(
                application: 'APP2',
                service: 'DEMO',
                code: 'COUNT',
                body: '${DateTime.now()}',
            )
        );


**Timer**: MAFTimer
* [ ]  MAFTimer() : Singleton instance (dart factory constructor)
* [ ]  register
* [ ]  unregister

>    **register**: Đăng ký một task - các task được phân biệt dựa trên tên (functionName)

*       MAFTimer().register(TimerType.Timer10s, () { 
            print('Perform Action');
        }, 'task 1');


    Ex2:
*       MAFTimer().register(TimerType.Timer10s, () {
            print('Perform Action');
        }, 'task 2', 
            cycle_sec: 5, 
            numberOfRun: 10, 
            timeout_sec: 0, 
            expireAt_ms: 0
        );

>    **unregister**: Huỷ task. *Ex: Huỷ task tên "task 1"*

*       MAFTimer().unregister('task 1');


**Communication**: MAFCommunicator
* [ ]  MAFCommunicator() : Singleton instance (dart factory constructor)
* [ ]  registerChannel
* [ ]  registerChannels
* [ ]  unregisterChannel
* [ ]  sendMessage
* [ ]  sendMessageToAll
* [ ]  sendEvent
* [ ]  close

> Comm  support một số kênh có sẵn, thuộc các loại channel service:

> Socket:
- WebSocket (class MAFWebSocket)

> Notification:
- Firebase message (class MAFFireBaseNotification)
- oneSignal (chưa cài đặt)

> Tracking:
- Firebase analytics (class MAFFirebaseAnalytics)

> Stream:
- Kinesis (chưa cài đặt)
- Kafka (chưa cài đặt)

> Custom: nếu bạn muốn cài đặt service riêng và không nằm trong 1 các loại trên

>  **registerChannel**: Đăng ký kênh
* [ ]  MAFWebSocket

*       MAFCommunicator().registerChannel(MAFWebSocket());

Note: Để kênh socket có thể wakeup thành công, set socket config xuống DataCenter theo format:

*       Map<String, dynamic> socketConfig = {
          'url': 'wss://jou1cu3bvg.execute-api.ap-southeast-1.amazonaws.com/dev',
          'ping_data': {
            'fastgoId': '34610084',
            'action': 'ping',
          },
          'ping_duration': Duration(seconds: 40).inMilliseconds,
        };
        dc.set(MAFWebSocket.socketConfigKey, json.encode(socketConfig));

>  **registerChannels**: Đăng ký danh sách các kênh

*       MAFCommunicator().registerChannel([ 
            MAFWebSocket(),
            MAFFireBaseNotification(),
            ... 
        ]);

>  **unregisterChannel**: Huỷ kênh

*       MAFCommunicator().unregisterChannel(aChannel);


>  **sendMessage**: Để gửi message, cần xác định data gửi đi đến loại dịch vụ nào

>  optionals: **channelName**: gửi chính xác đến 1 kênh cụ thể.

*       MAFCommunicator().channelService: ChannelService.SOCKET, 
            data: {
              'fastGoId': '1234'
            }
        );


>  **sendMessageToAll**: Gửi message tới toàn bộ các kênh

*       MAFCommunicator().sendMessageToAll({'message':'Hello world'});

>  **sendEvent**: Gửi event tới các kênh tracking

>  optionals: **channelName**: gửi chính xác đến 1 kênh cụ thể.

> Ví dụ: gửi các event tracking (login_in, sign_up,run_app, ...)

*       MAFCommunicator().sendEvent(
            channelService: ChannelService.ANALYTIC,
            eventName: 'sign_up',
            data: {'time': 12345678}
        );

>  **close**: Ngắt kết nối

*       MAFCommunicator().close();
<!--Tracking-->

**Format hợp lệ server cần trả về cho Comm**

>  optionals: **expiredTime**, **singleMessageOnly**

> nếu trả về singleMessageOnly là true thì messageId phải khác null

*       {
            'messageId': String,
            'expiredTime': int,
            'singleMessageOnly': boolean,
            'application': String,
            'service': String,
            'code': String,
            'priority': int (value trong khoảng 1 -> 5)
            'body': object (string / map<string, object>)
        }

**Cài đặt kênh và tích hợp vào Communicator**

Tối thiểu cần cài đặt driver CommunicatorChannel để có thể tích hợp chung vào bộ Communicator:

*       class MyChannel implements CommunicatorChannel {
            @override
            void subscribedToMAFComm(maf.CommMessageHandler mafMessageHandler) {
            }
            @override
            String getChannelName() {
                return NAME;
            }
            @override
            ChannelService getChannelService() {
                return ChannelService.SOCKET;
            }
            @override void sendMessage(Map<String, Object> data) {
            }
            @override void close() {
            }
            @override
            Future<bool> wakeUp() async {
                return true;
            }
        }

Nếu kênh cần support tracking, implement thêm driver EventTracking:

*       class MyChannel implements CommunicatorChannel, EventTracking {
            ...
            @override void logEvent(String eventName, Map<String, Object> data) {
            }
        }

Nếu kênh cần cơ chế ping pong, implement PingPongInterface:

*       class MyChannel implements CommunicatorChannel, PingPongInterface {
            ...
            @override
                void ping(String uuid) {
            }
        }

## Tích hợp: 

*Framework được tạo theo kiến trúc tạo package, trong project (pubspec.yaml) khai báo depencencies để tích hợp vào trong project*


*       dependencies:
          maf:
            git:
              url: git@gitlab.saobang.vn:maf/mobile_app_framework.git

libyangpeerconnection7编程指南

# 依赖文件

## 头文件

YangPeerConnection7.h

## 库文件

libyangpeerconnection7.so

# 示例代码

## 对象分配与释放

YangPeerConnection7* peer = (YangPeerConnection7*)calloc(1, sizeof(YangPeerConnection7));

YangPeerInfo* peerInfo=&peer->peer.peerInfo;

yang_init_peerParam(peerInfo);

peerInfo->uid = uid;

peerInfo->userId=userId;//查看配置文件sys/userId

peerInfo->direction = YangSendonly;

peerInfo->rtc.rtcLocalPort = 16000;

peerInfo->rtc.isControlled = yangtrue;

peerInfo->rtc.iceCandidateType = 2;

peerInfo->pushAudio.sample = 8000;

peerInfo->pushAudio.channel = 1;

peerInfo->pushAudio.audioEncoderType = Yang_AED_PCMA;

peerInfo->pushVideo.width = 1280;

peerInfo->pushVideo.height = 720;

peerInfo->pushVideo.fps = 30;

peerInfo->pushVideo.videoEncoderType = Yang_VED_H264;

peerInfo->rtc.iceServerPort=3478;

strcpy(peerInfo->rtc.iceServerIP, "192.168.0.104");

strcpy(peerInfo->rtc.iceUserName, "metartc");

strcpy(peerInfo->rtc.icePassword, "metartc");

peer->peer.peerCallback.sslCallback.context = session;

peer->peer.peerCallback.sslCallback.sslAlert = ipc_rtcrecv_sslAlert;

peer->peer.peerCallback.recvCallback.context = session;

peer->peer.peerCallback.recvCallback.receiveAudio = ipc_rtcrecv_receiveAudio;

peer->peer.peerCallback.recvCallback.receiveVideo = ipc_rtcrecv_receiveVideo;

peer->peer.peerCallback.recvCallback.receiveMsg = ipc_rtcrecv_receiveMsg;

peer->peer.peerCallback.iceCallback.context = session;

peer->peer.peerCallback.iceCallback.onConnectionStateChange=ipc_onConnectionStateChange;

peer->peer.peerCallback.iceCallback.onIceStateChange=ipc_onIceStateChange;

peer->peer.peerCallback.iceCallback.onIceCandidate=ipc_onIceCandidate;

yang_create_peerConnection7(peer);

peer->addAudioTrack(&peer->peer,Yang_AED_PCMA);

peer->addVideoTrack(&peer->peer,Yang_VED_H264);

peer->addTransceiver(&peer->peer,YangMediaAudio,peer->peer.peerInfo.direction);

peer->addTransceiver(&peer->peer,YangMediaVideo,peer->peer.peerInfo.direction);

//通过信令取得对端sdp

if((ret = peer->setRemoteDescription(&peer->peer, sdp))!=Yang_Ok){

goto cleanup;

}

//取得answer通过信令传回对端

if((ret = peer->createAnswer(&peer->peer, answer))!=Yang_Ok){

goto cleanup;

}

//free peer

if(peer){

    yang_destroy_peerConnection7(peer);

    free(peer);

}

## 交换Candidate

//通过信令取得对端candidate

rtc->addIceCandidate(&rtc->peer,candidate);

//回调函数
static void ipc_onIceCandidate(void* context, int32_t uid,char* sdp){
    if (context == NULL) return;
    //取得candidate 通过信令传到对端
}

## 回调函数

static void ipc_onConnectionStateChange(void* context, int32_t uid,YangRtcConnectionState connectionState){

if (context == NULL) return;

switch(connectionState){

case Yang_Conn_State_Connecting:{

break;

}

case Yang_Conn_State_Connected:{

break;

}

case Yang_Conn_State_Disconnected:{

break;

}

case Yang_Conn_State_Failed:{

//yang_trace("\nYang_Conn_State_Failed remove uid==%d",uid);

//removePeer(uid);

break;

}

case Yang_Conn_State_Closed:{

break;

}

default:break;

}

}

static void ipc_onIceStateChange(void* context,int32_t uid,YangIceCandidateState iceState){

if (context == NULL) return;

switch(iceState){

case YangIceSuccess:{

break;

}

case YangIceFail:{

//yang_trace("\nYangIceFail remove uid==%d",uid);

break;

}

default:break;

}

}

static void ipc_onIceCandidate(void* context, int32_t uid,char* sdp){

    if (context == NULL) return;

    //取得candidate 传到对端

}

static void ipc_rtcrecv_sslAlert(void *context, int32_t uid, char *type, char *desc) {

    if (context == NULL || type == NULL || desc == NULL)

        return;

    if (yang_strcmp(type, "warning") == 0 && yang_strcmp(desc, "CN") == 0) {

        removePeer( uid);

    }

}

static void ipc_rtcrecv_receiveAudio(void *user, YangFrame *audioFrame) {

    if (user == NULL || audioFrame == NULL)

    return;

}

static void ipc_rtcrecv_receiveVideo(void *user, YangFrame *videoFrame) {

    if (user == NULL || videoFrame == NULL)

    return;

}

static void ipc_rtcrecv_receiveMsg(void *user, YangFrame *msgFrame) {

    if (user == NULL) return;

  //  receiveMsg(msgFrame->uid,msgFrame->payload,msgFrame->nb);

}

## 推流

//YangFrame payload 数据 nb:数据长度 

//frametype:YANG_Frametype_I I帧  四字节sps长度+sps+四字节pps长度+pps+四字节I帧//长度+I帧 如：00,00,00,0e,67,42,c0,1f,8c,8d,40,50,1e,d0,0f,08,84,6a,00,00,00,04,68,ce,3c,80,00,00,00,01,65,b8,00,04,00,00,13,88,c5

//YANG_Frametype_P非I帧 如：00,00,00,01,61,e0,00,40,00,be,41,38,22,93,df

//去掉start code 00,00,00,01,取61,e0,00,40,00,be,41,38,22,93,df

rtc->on_video(&rtc->peer, frame)://推视频

rtc->on_audio(&rtc->peer, frame);//推音频

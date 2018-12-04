# Uneral Engine4
## 昌

## Unreal Engine and MACTalonPlugin

### MACTalonPlugin
1. **插件的安装**   
MACTalonPlugin对应不同版本的Unreal Engine此处参考MACTalonPlugin相应的说明文档。  
安装路径：./UnrealDirectory/Engine/ 对应下的目录。
2. **加载插件**   

## UE4实用蓝图技巧
### 蓝图节点说明
#### Create Save Game Object
创建游戏保存实例
#### Does Save Game Exist
查找是否保存过游戏
#### Left Chop
从右到左剪切掉Count指定的字符串。
#### To Upper
将字符串转换成大写字母。
#### To Lower
将字符串转换成小写字母。
## C++代码案例
### UE4与WEB服务器交互(json)###
概述
制作游戏在很多情况下需要和WEB服务器进行交互，最常见的是在做Demo时需要通过游戏向WEB服务器传递数据（登录/注册验请求），WEB服务器处理（操作数据库）之后返回结果并调用指定的方法。 该教程简单介绍了如何通过UE4向WEB服务器（PHP）发送json数据包及回调方法。

添加模块和头文件引用
在代码编辑器中打开项目解决方案，在<Solution Name>/Source/< ProjectName>路径下，找到并打开< ProjectName>.Build.cs文件，添加HTTP模块：

	PrivateDependencyModuleNames.AddRange(new string[] {"HTTP"}); 
	PrivateIncludePathModuleNames.AddRange(new string[] {"HTTP"});

然后在需要实现该功能的类文件中添加如下的头文件引用:

```c++
#include "Http.h"
#include "Json.h"
```

创建json数据包
数据内容为：

```javascript
[javascript]
{"user":"StormUnited"]
```

创建：

```c++
// Create a writer and hold it in this FString
FString JsonStr;
TSharedRef< TJsonWriter<TCHAR, TCondensedJsonPrintPolicy<TCHAR> > > JsonWriter = TJsonWriterFactory<TCHAR, TCondensedJsonPrintPolicy<TCHAR> >::Create(&JsonStr);
JsonWriter->WriteObjectStart();
JsonWriter->WriteValue(TEXT("user"), TEXT("StormUnited"));
JsonWriter->WriteObjectEnd();
 
// Close the writer and finalize the output such that JsonStr has what we want
JsonWriter->Close();
```

至此，json数据包准备完成。

准备接收json数据包的PHP网页
本示例中使用了PHP，你可以选择使用搭建动态网站或者服务器的开源软件，比如说wamp/lamp等在本机上建立一个WEB服务器来解析PHP页面。 创建mywebpage.php文件，并添加如下代码：

```php
<?php
     // 首先接收上传的数据
     $post_data = file_get_contents('php://input');
     // 解析json字符串
     $obj = json_decode($post_data);
     // 获取包含在Json字符串中的数据
     echo $obj->{'user'};
?>
```

POST数据
将通过如下的代码将上面准备好的json数据包提交给 <http://localhost/mywebpage.php>
SetHeader：可以设置POST数据的格式
SetURL：可以指定用于处理上传数据的链接
SetVerb：可以设置POST/PUT/GET
SetContentAsString：用于填充上传的数据内容
OnProcessRequestComplete().BindUObject 用于指定在发送请求之后的回调方法。

```c++
TSharedRef<IHttpRequest> HttpRequest = FHttpModule::Get().CreateRequest();
HttpRequest->SetHeader(TEXT("Content-Type"), TEXT("application/json; charset=utf-8"));
HttpRequest->SetURL(TEXT("http://localhost/mywebpage.php"));
HttpRequest->SetVerb(TEXT(" POST"));
HttpRequest->SetContentAsString(JsonStr);
HttpRequest->OnProcessRequestComplete().BindUObject(this, &ASUMiniGameMode::HttpCompleteCallback);
HttpRequest-> ProcessRequest();
```

关于回调函数的结构：

```c++
void HttpCompleteCallback(FHttpRequestPtr Request, FHttpResponsePtr Response, bool bWasSuccessful); 示例：
void ASUMiniGameMode::HttpCompleteCallback(FHttpRequestPtr Request, FHttpResponsePtr Response, bool bWasSuccessful)
{
     FString MessageBody = "";
 
     // If HTTP fails client-side, this will still be called but with a NULL shared pointer!
     if (!Response.IsValid())
     {
          MessageBody = "{\"success\":\"Error: Unable to process HTTP Request!\"}";
     }
     else if (EHttpResponseCodes::IsOk(Response->GetResponseCode()))
     {
          MessageBody = Response->GetContentAsString();
     }
     else
     {
          MessageBody = FString:: Printf(TEXT("{\"success\":\"HTTP Error: %d\"}"), Response->GetResponseCode());
     }
}
```

一旦发送出请求后肯定会调用HttpCompleteCallback方法，WEB服务器处理的数据结果包含在Response参数中，可以通过Response->GetContentAsString()来获取返回的字符串，比如在本例中是StormUnited。

###UE4读取CSV表格配置数据###
开发游戏时，经常会采用表格文件来存储游戏相关配置数，比如玩家的最大健康值、魔法值等，以及NPC的属性配置等。在Unreal下，无须自己编写表格文件的读取及解析代码，可以直接使用Unreal自带的读取表格数据功能。
下面通过一个简单示例来说明如何通过Unreal来读取CVS格式的表格数据。
表格数据示例如下：
![image](http://img.blog.csdn.net/20160809112032097?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)  
注意，表格第一个单元格必须为空，第一行为每一列的名称，该行数据也是存取每一列数据的唯一标识。表格第一列代表行ID，获取某行数据时需要根据第一列的值标识某一行数据。

首先，创建一个Actor类，头文件中添加表格变量，程序通过该Actor对象来读取表格数据。代码如下：

```c++
  UCLASS()
class CVSTABLE_API ATableRowSetting : public AActor
{
    GENERATED_BODY()

public:
    // Sets default values for this actor's properties
    ATableRowSetting();

    // Called when the game starts or when spawned
    virtual void BeginPlay() override;

    // Called every frame
    virtual void Tick( floatDeltaSeconds )override;

    UPROPERTY(BlueprintReadWrite,EditAnywhere,Category ="Data SettingActor")
        UDataTable* DataTable;

};
```
然后在该头文件中添加与该CSV文件对应的表格数据结构，该结构从FTableRowBase结构派生，代码如下：

```c++
USTRUCT(BlueprintType)
struct FTableSettingData :publicFTableRowBase
{
    GENERATED_USTRUCT_BODY()

        UPROPERTY(BlueprintReadOnly,Category ="TableSetting")
        int32 MAX_HP;

    UPROPERTY(BlueprintReadOnly,Category ="TableSetting")
        int32 MAX_MP;

    UPROPERTY(BlueprintReadOnly,Category ="TableSetting")
        int32 MAX_LEVEL;

    UPROPERTY(BlueprintReadOnly,Category ="TableSetting")
        int32 MAX_XP;

};
```
注意，该结构中的属性变量必须与CSV文件中第一行中的列名保持一致。
然后在BeginPlay函数中实现表格数据的读取，代码如下：

```c++
void ATableRowSetting::BeginPlay()
{
    Super::BeginPlay();

    if (DataTable !=nullptr)
    {
        FTableSettingData* dataRow = DataTable->FindRow<FTableSettingData>(TEXT("HUMAN"),TEXT("LookupRow"));

        int32  HP = dataRow->MAX_HP;

        UE_LOG(LogTemp, Warning, TEXT(" Max HP is %d "),HP);
    }

}
```
至此，读取表格数据的代码工作已经全部完成，接下来需要在编辑器中完成必要的工作。
首先，将创建好的CSV文件拖至编辑器进行导入，导入时，会弹出提示框，选择DataTable，以及刚才定义的表格数据数据结构。
接下来选择刚创建的ATableRowSetting类资源，在属性中可以看到DataTable属性，将导入的CSV文件资源拖至该属性，保存工程并运行即可看到结果。
###关于如何通过定义自己的CameraManager来控制视角###
最近看了几天PlayerCameraManager的代码，大致看明白了，本着分享的原则，在此分享一些经验。
PlayerCameraManager，顾名思义就是管理角色摄像仪与视角的，你可以通过继承的方式，编写自己的PlayerCameraManager，之后在你角色的控制类中指定即可。
下面说一下主要的运行过程：
这里可以参考一下ShooterGame(ShooterPlayerCameraManager),从函数UpdateCamera开始看

```c++
    // Make sure view target is valid
    if( NewTarget == NULL )
    {
            NewTarget = PCOwner;
    }
 
    // Update current ViewTargets
    ViewTarget.CheckViewTarget(PCOwner);
    if( PendingViewTarget.Target )
    {
            PendingViewTarget.CheckViewTarget(PCOwner);
    }
 
    // If we're already transitioning to this new target, don't interrupt.
    if( PendingViewTarget.Target != NULL && NewTarget == PendingViewTarget.Target )
    {
            return;
    }
```

这里并没有视角的相关逻辑，所以进一步看父类的UpdateCamera函数。

```c++
void APlayerCameraManager::UpdateCamera(float DeltaTime)
{
    if ((PCOwner->Player && PCOwner->IsLocalPlayerController()) || !bUseClientSideCameraUpdates || bDebugClientSideCamera)
    {
        DoUpdateCamera(DeltaTime);
 
        if (GetNetMode() == NM_Client && bShouldSendClientSideCameraUpdate)
        {
            // compress the rotation down to 4 bytes
            int32 const ShortYaw = FRotator::CompressAxisToShort(CameraCache.POV.Rotation.Yaw);
            int32 const ShortPitch = FRotator::CompressAxisToShort(CameraCache.POV.Rotation.Pitch);
            int32 const CompressedRotation = (ShortYaw << 16) | ShortPitch;
 
            PCOwner->ServerUpdateCamera(CameraCache.POV.Location, CompressedRotation);
            bShouldSendClientSideCameraUpdate = false;
        }
    }
}
```
这里是一些网络的逻辑，继续看DoUpdateCamera函数

	这里是一些网络的逻辑，继续看DoUpdateCamera函数

接下来看UpdateViewTarget函数，计算视角的逻辑都在里面

```c++
void APlayerCameraManager::UpdateViewTarget(FTViewTarget& OutVT, float DeltaTime)
{
    // Don't update outgoing viewtarget during an interpolation 
    if ((PendingViewTarget.Target != NULL) && BlendParams.bLockOutgoing && OutVT.Equal(ViewTarget))
    {
        return;
    }
　　//设置默认属性的摄像机
    // store previous POV, in case we need it later
    FMinimalViewInfo OrigPOV = OutVT.POV;
　　
    //@TODO: CAMERA: Should probably reset the view target POV fully here
    OutVT.POV.FOV = DefaultFOV;
    OutVT.POV.OrthoWidth = DefaultOrthoWidth;
    OutVT.POV.bConstrainAspectRatio = false;
    OutVT.POV.bUseFieldOfViewForLOD = true;
    OutVT.POV.ProjectionMode = bIsOrthographic ? ECameraProjectionMode::Orthographic : ECameraProjectionMode::Perspective;
    OutVT.POV.PostProcessSettings.SetBaseValues();
    OutVT.POV.PostProcessBlendWeight = 1.0f;
```


```c++
    bool bDoNotApplyModifiers = false;
　　//如果ViewTarget是个摄像机的话，就直接获取摄像机的视角
    if (ACameraActor* CamActor = Cast<ACameraActor>(OutVT.Target))
    {
        // Viewing through a camera actor.
        CamActor->GetCameraComponent()->GetCameraView(DeltaTime, OutVT.POV);
    }
    else
    {
　　　　//下面都是一些不同模式的摄像机的逻辑，你可以在游戏中按下~，输入Camera XXXX的方式切换这个摄像机
　　　　//默认的摄像机是Default
 
        static const FName NAME_Fixed = FName(TEXT("Fixed"));
        static const FName NAME_ThirdPerson = FName(TEXT("ThirdPerson"));
        static const FName NAME_FreeCam = FName(TEXT("FreeCam"));
        static const FName NAME_FreeCam_Default = FName(TEXT("FreeCam_Default"));
        static const FName NAME_FirstPerson = FName(TEXT("FirstPerson"));
 
        if (CameraStyle == NAME_Fixed)
        {
            // do not update, keep previous camera position by restoring
            // saved POV, in case CalcCamera changes it but still returns false
            OutVT.POV = OrigPOV;
 
            // don't apply modifiers when using this debug camera mode
            bDoNotApplyModifiers = true;
        }
        else if (CameraStyle == NAME_ThirdPerson || CameraStyle == NAME_FreeCam || CameraStyle == NAME_FreeCam_Default)
        {
            // Simple third person view implementation
            FVector Loc = OutVT.Target->GetActorLocation();
            FRotator Rotator = OutVT.Target->GetActorRotation();
 
            if (OutVT.Target == PCOwner)
            {
                Loc = PCOwner->GetFocalLocation();
            }
 
            // Take into account Mesh Translation so it takes into account the PostProcessing we do there.
            // @fixme, can crash in certain BP cases where default mesh is null
//            APawn* TPawn = Cast<APawn>(OutVT.Target);
//             if ((TPawn != NULL) && (TPawn->Mesh != NULL))
//             {
//                 Loc += FQuatRotationMatrix(OutVT.Target->GetActorQuat()).TransformVector(TPawn->Mesh->RelativeLocation - GetDefault<APawn>(TPawn->GetClass())->Mesh->RelativeLocation);
//             }
 
            //OutVT.Target.GetActorEyesViewPoint(Loc, Rot);
            if( CameraStyle == NAME_FreeCam || CameraStyle == NAME_FreeCam_Default )
            {
                Rotator = PCOwner->GetControlRotation();
            }
 
            FVector Pos = Loc + ViewTargetOffset + FRotationMatrix(Rotator).TransformVector(FreeCamOffset) - Rotator.Vector() * FreeCamDistance;
            FCollisionQueryParams BoxParams(NAME_FreeCam, false, this);
            BoxParams.AddIgnoredActor(OutVT.Target);
            FHitResult Result;
 
            GetWorld()->SweepSingleByChannel(Result, Loc, Pos, FQuat::Identity, ECC_Camera, FCollisionShape::MakeBox(FVector(12.f)), BoxParams);
            OutVT.POV.Location = !Result.bBlockingHit ? Pos : Result.Location;
            OutVT.POV.Rotation = Rotator;
 
            // don't apply modifiers when using this debug camera mode
            bDoNotApplyModifiers = true;
        }
        else if (CameraStyle == NAME_FirstPerson)
        {
            // Simple first person, view through viewtarget's 'eyes'
            OutVT.Target->GetActorEyesViewPoint(OutVT.POV.Location, OutVT.POV.Rotation);
     
            // don't apply modifiers when using this debug camera mode
            bDoNotApplyModifiers = true;
        }
        else
        {
　　　　　　　//默认摄像机会执行这个函数
            UpdateViewTargetInternal(OutVT, DeltaTime);
        }
    }
　　//这个应该是执行CameraShakes的逻辑
    if (!bDoNotApplyModifiers || bAlwaysApplyModifiers)
    {
        // Apply camera modifiers at the end (view shakes for example)
        ApplyCameraModifiers(DeltaTime, OutVT.POV);
    }
　　//头戴设备的视角逻辑
    if (bFollowHmdOrientation)
    {
        if (GEngine->HMDDevice.IsValid() && GEngine->HMDDevice->IsHeadTrackingAllowed())
        {
            GEngine->HMDDevice->UpdatePlayerCameraRotation(this, OutVT.POV);
        }
    }
 
    // Synchronize the actor with the view target results
    SetActorLocationAndRotation(OutVT.POV.Location, OutVT.POV.Rotation, false);
　　
    UpdateCameraLensEffects(OutVT);
}
```
综上所述，你应该把摄像机逻辑写在这，如果想了解得更加清楚，可以继续从UpdateViewTargetInternal函数看下去，一直看到CalcCamera为止。
下面大致说一下编写思路：
在使用编辑器新建完自己的CameraManager后，在控制类中制定，例如：
PlayerCameraManagerClass = ADemoCameraManager::StaticClass();
然后我贴一下自己写的代码，这里我实现了一个固定视角：

	// Fill out your copyright notice in the Description page of Project Settings.

	#pragma once

```c++
#include "Camera/PlayerCameraManager.h"
#include "DemoCameraManager.generated.h"
 
/**
 * 
 */
UCLASS()
class DEMO_API ADemoCameraManager : public APlayerCameraManager
{
    GENERATED_BODY()
protected:
    virtual void UpdateViewTarget(FTViewTarget& OutVT, float DeltaTime) override;
     
    //UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = TViewTarget)
    //struct FMinimalViewInfo SceneFixedPOV;
     
};
```

------------------------

	// Fill out your copyright notice in the Description page of Project Settings.

```c++
#include "Demo.h"
#include "DemoCameraManager.h"
#include "DemoCharacter.h"
 
void ADemoCameraManager::UpdateViewTarget(FTViewTarget& OutVT, float DeltaTime)
{
    if (bFollowHmdOrientation)
    {
        //如果有VR设备就直接运行引擎原始函数
        Super::UpdateViewTarget(OutVT, DeltaTime);
        return;
    }
    // Don't update outgoing viewtarget during an interpolation 
    if ((PendingViewTarget.Target != NULL) && BlendParams.bLockOutgoing && OutVT.Equal(ViewTarget))
    {
        return;
    }
    bool bDoNotApplyModifiers = false;
 
    if (ACameraActor* CamActor = Cast<ACameraActor>(OutVT.Target))
    {
        // Viewing through a camera actor.
        CamActor->GetCameraComponent()->GetCameraView(DeltaTime, OutVT.POV);
    }
    else
    {
        if (CameraStyle == FName("SceneFixed"))
        {
　　　　　　　//这里我感觉可能用PendingViewTarget来传递摄像机坐标和旋转比较好，但是还没测试过
            //自己定义一个场景固定视角
            ADemoCharacter *Character = Cast<ADemoCharacter>(GetOwningPlayerController()->GetPawn());
            OutVT.POV.Location = Character->ViewTargetLocation;
            OutVT.POV.Rotation = Character->ViewTargetRotator;
            //DesiredView.FOV = FieldOfView;
            //DesiredView.AspectRatio = AspectRatio;
            // don't apply modifiers when using this debug camera mode
            bDoNotApplyModifiers = true;
        }else if (CameraStyle==FName("Default"))
        {
            //默认方式是直接取得摄像机的参数来设置FTViewTarget.pov,而摄像机被控制类、SpringArm控制。
            UpdateViewTargetInternal(OutVT, DeltaTime);
        }
        else
        {
            Super::UpdateViewTarget(OutVT, DeltaTime);
        }
    }
    if (!bDoNotApplyModifiers || bAlwaysApplyModifiers)
    {
        // Apply camera modifiers at the end (view shakes for example)
        ApplyCameraModifiers(DeltaTime, OutVT.POV);
    }
    // Synchronize the actor with the view target results
    SetActorLocationAndRotation(OutVT.POV.Location, OutVT.POV.Rotation, false);
    UpdateCameraLensEffects(OutVT);
}
```
后面是固定视角VolumeActor的代码：

	// Fill out your copyright notice in the Description page of Project Settings.

	#pragma once

```c++
#include "GameFramework/Actor.h"
#include "Character/DemoCharacter.h"
#include "Character/DemoCameraManager.h"
#include "Character/DemoPlayerController.h"
#include "Runtime/Engine/Classes/Kismet/KismetMathLibrary.h"
#include "BaseSceneFixedViewVolume.generated.h"
 
UCLASS()
class DEMO_API ABaseSceneFixedViewVolume : public AActor
{
    GENERATED_BODY()
     
public:    
    // Sets default values for this actor's properties
    ABaseSceneFixedViewVolume();
 
    // Called when the game starts or when spawned
    virtual void BeginPlay() override;
     
    // Called every frame
    virtual void Tick( float DeltaSeconds ) override;
 
    FVector Lcation;
    FRotator Rotator;
 
    UPROPERTY(EditDefaultsOnly, Category = "SceneFixedView", meta = (AllowPrivateAccess = "true"))
    UBoxComponent* Triggers;
 
    UPROPERTY(VisibleAnywhere, BlueprintReadWrite, Category = "SceneFixedView", meta = (AllowPrivateAccess = "true"))
    UCameraComponent* Camera;
```


```c++
    UFUNCTION()
    virtual void OnBeginOverlap(class AActor* OtherActor, class UPrimitiveComponent* OtherComp, int32 OtherBodyIndex, bool bFromSweep, const FHitResult& SweeoResult);
 
    UFUNCTION()
    virtual void OnEndOverlap(class AActor* OtherActor, class UPrimitiveComponent* OtherComp, int32 OtherBodyIndex);
 
    UPROPERTY(VisibleAnywhere, BlueprintReadWrite, Category = "SceneFixedView")
    bool bCalView=false;
 
    UPROPERTY(EditDefaultsOnly,Category="SceneFixedView")
    FVector VolumeSize = FVector(500, 500, 200);
 
    ADemoCharacter* Character;
 
    ADemoCameraManager* CameraManage;
 
    FName OriginMode;
};
```

-------------------------

	// Fill out your copyright notice in the Description page of Project Settings.

	#include "Demo.h"
	#include "BaseSceneFixedViewVolume.h"


​	 
	// Sets default values
	ABaseSceneFixedViewVolume::ABaseSceneFixedViewVolume()
	{
	     // Set this actor to call Tick() every frame.  You can turn this off to improve performance if you don't need it.
	    PrimaryActorTick.bCanEverTick = true;
	 
	    Triggers = CreateDefaultSubobject<UBoxComponent>(TEXT("Triggers"));
	    Triggers->SetBoxExtent(VolumeSize);
	    Triggers->SetRelativeLocation(FVector(0,0,VolumeSize.Z/2));
	    RootComponent = Triggers;
	 
	    Camera= CreateDefaultSubobject<UCameraComponent>(TEXT("Camera"));
	    Camera->SetRelativeLocation(FVector(VolumeSize.X,VolumeSize.Y,VolumeSize.Z));
	    Camera->AttachTo(RootComponent);
	 
	    //FollowCamera = CreateDefaultSubobject<UCameraComponent>(TEXT("FollowCamera"));
	    //FollowCamera->AttachTo(CameraBoom, USpringArmComponent::SocketName); // Attach the camera to the end of the boom and let the boom adjust to match the controller orientation
	    //FollowCamera->bUsePawnControlRotation = false; // Camera does not rotate relative to arm


	    Triggers->OnComponentBeginOverlap.AddDynamic(this, &ABaseSceneFixedViewVolume::OnBeginOverlap);
	    Triggers->OnComponentEndOverlap.AddDynamic(this, &ABaseSceneFixedViewVolume::OnEndOverlap);
	}
	 
	// Called when the game starts or when spawned
	void ABaseSceneFixedViewVolume::BeginPlay()
	{
	    Super::BeginPlay();
	     
	}
	 
	// Called every frame
	void ABaseSceneFixedViewVolume::Tick( float DeltaTime )
	{
	    Super::Tick( DeltaTime );
	    if (bCalView)
	    {
	        if (Character)
	        {
	            Character->ViewTargetLocation = Camera->K2_GetComponentLocation();
	            Character->ViewTargetRotator = UKismetMathLibrary::FindLookAtRotation(Camera->K2_GetComponentLocation(), Character->GetActorLocation());
	        }
	    }
	}
	void ABaseSceneFixedViewVolume::OnBeginOverlap(class AActor* OtherActor, class UPrimitiveComponent* OtherComp, int32 OtherBodyIndex, bool bFromSweep, const FHitResult& SweeoResult)
	{
	    Character = Cast<ADemoCharacter>(OtherActor);
	    if (Character)
	    {
	        bCalView = true;
	        ADemoPlayerController* Controller = Cast<ADemoPlayerController>(Character->GetController());
	        if (Controller)
	        {
	            OriginMode=Controller->PlayerCameraManager->CameraStyle;
	            Controller->SetCameraMode(FName("SceneFixed"));
	            //因为我使用控制类来管理视角，为了解决一些视角问题，所以在进入时，必须重新设置视角,屏蔽鼠标修改视角
	            Controller->SetControlRotation(FRotator(0.0f,90.0f,0.0f));
	            Controller->SetIgnoreLookInput(true);
	        }
	    }
	}
	 
	void ABaseSceneFixedViewVolume::OnEndOverlap(class AActor* OtherActor ,class UPrimitiveComponent* OtherComp, int32 OtherBodyIndex)
	{
	    if (OtherActor==Character)
	    {
	        bCalView = false;
	        ADemoPlayerController* Controller = Cast<ADemoPlayerController>(Character->GetController());
	        if (Controller)
	        {
	            if (OriginMode!=FName(""))
	            {
	                Controller->SetCameraMode(OriginMode);
	            }
	            else
	            {
	                Controller->SetCameraMode(FName("Default"));
	            }
	            Controller->SetIgnoreLookInput(false);
	        }
	    }
	    Character= nullptr;
	}
因为代码太多了，DoUpdateCamera的代码补上

	void APlayerCameraManager::DoUpdateCamera(float DeltaTime)
	{
	　　//一些后期效果融合
	    // update color scale interpolation
	    if (bEnableColorScaleInterp)
	    {
	        float BlendPct = FMath::Clamp((GetWorld()->TimeSeconds - ColorScaleInterpStartTime) / ColorScaleInterpDuration, 0.f, 1.0f);
	        ColorScale = FMath::Lerp(OriginalColorScale, DesiredColorScale, BlendPct);
	        // if we've maxed
	        if (BlendPct == 1.0f)
	        {
	            // disable further interpolation
	            bEnableColorScaleInterp = false;
	        }
	    }


	　　//是否停止工作，不然就执行UpdateViewTarget
	    // Don't update outgoing viewtarget during an interpolation when bLockOutgoing is set.
	    if ((PendingViewTarget.Target == NULL) || !BlendParams.bLockOutgoing)
	    {
	        // Update current view target
	        ViewTarget.CheckViewTarget(PCOwner);
	        UpdateViewTarget(ViewTarget, DeltaTime);
	    }
	 
	　　//下面都是一些视角更新的循环逻辑，看到最后你会发现他们一直都是用的引用，设置新的视角的逻辑并不在这
	    // our camera is now viewing there
	    FMinimalViewInfo NewPOV = ViewTarget.POV;
	 
	    // if we have a pending view target, perform transition from one to another.
	    if (PendingViewTarget.Target != NULL)
	    {
	        BlendTimeToGo -= DeltaTime;
	 
	        // Update pending view target
	        PendingViewTarget.CheckViewTarget(PCOwner);
	        UpdateViewTarget(PendingViewTarget, DeltaTime);
	 
	        // blend....
	        if (BlendTimeToGo > 0)
	        {
	            float DurationPct = (BlendParams.BlendTime - BlendTimeToGo) / BlendParams.BlendTime;
	 
	            float BlendPct = 0.f;
	            switch (BlendParams.BlendFunction)
	            {
	            case VTBlend_Linear:
	                BlendPct = FMath::Lerp(0.f, 1.f, DurationPct);
	                break;
	            case VTBlend_Cubic:
	                BlendPct = FMath::CubicInterp(0.f, 0.f, 1.f, 0.f, DurationPct);
	                break;
	            case VTBlend_EaseIn:
	                BlendPct = FMath::Lerp(0.f, 1.f, FMath::Pow(DurationPct, BlendParams.BlendExp));
	                break;
	            case VTBlend_EaseOut:
	                BlendPct = FMath::Lerp(0.f, 1.f, FMath::Pow(DurationPct, 1.f / BlendParams.BlendExp));
	                break;
	            case VTBlend_EaseInOut:
	                BlendPct = FMath::InterpEaseInOut(0.f, 1.f, DurationPct, BlendParams.BlendExp);
	                break;
	            default:
	                break;
	            }
	 
	            // Update pending view target blend
	            NewPOV = ViewTarget.POV;
	            NewPOV.BlendViewInfo(PendingViewTarget.POV, BlendPct);//@TODO: CAMERA: Make sure the sense is correct!  BlendViewTargets(ViewTarget, PendingViewTarget, BlendPct);
	        }
	        else
	        {
	            // we're done blending, set new view target
	            ViewTarget = PendingViewTarget;
	 
	            // clear pending view target
	            PendingViewTarget.Target = NULL;
	 
	            BlendTimeToGo = 0;
	 
	            // our camera is now viewing there
	            NewPOV = PendingViewTarget.POV;
	        }
	    }
	 
	    // Cache results
	    FillCameraCache(NewPOV);
	 
	    if (bEnableFading)
	    {
	        if (bAutoAnimateFade)
	        {
	            FadeTimeRemaining = FMath::Max(FadeTimeRemaining - DeltaTime, 0.0f);
	            if (FadeTime > 0.0f)
	            {
	                FadeAmount = FadeAlpha.X + ((1.f - FadeTimeRemaining / FadeTime) * (FadeAlpha.Y - FadeAlpha.X));
	            }
	 
	            if ((bHoldFadeWhenFinished == false) && (FadeTimeRemaining <= 0.f))
	            {
	                // done
	                StopCameraFade();
	            }
	        }
	 
	        if (bFadeAudio)
	        {
	            ApplyAudioFade();
	        }
	    }
	}

###UE4链接第三方库(lib and dll)###
摘要：写这个文章主要是被UE官方的wiki和answerhub误导了很久，这本来是一个很常见和基本的问题，但是无论是官方的wiki或者是论坛上的提问都十分散乱并且充斥各种错误，因此记录下这个在开发中时常遇到的问题。

在开发中经常遇到的问题就是加入某第三方库的支持，这样的第三方库往往属于无源码，而且可能是静态lib或者是动态dll甚至两者皆有。UE4的编译管理用的是自己的UBT(unreal binary tool)因此链接第三方库的工作主要是编写UBT脚本。

1. 以插件方式集成. 
  
基本上这个是最推荐的集成第三方库的方式，因为能够很好的隔离你的代码和第三方代码的影响，在UE4的源码里也可以看到很多第三方库都是这么集成的，比如paper2D，leapmotion等等。在UE4中新建插件的方式略去不表，当你新建完你的插件之后，你会在插件的代码目录下看到一个

		xxx.build.cs

接下来要做的就是修改这个脚本：

得到当前路径

	private string ModulePath
	{
	   get { return ModuleDirectory; }
	}
关于第三方库放的位置，一般是在plugin的源码同级文件夹下建一个ThirdParty文件夹，里面放上include lib等等。

得到ThirdParty文件夹的路径

	private string ThirdPartyPath
	{
	        get { return Path.GetFullPath(Path.Combine(ModulePath,"../../ThirdParty/")); }
	}
为工程添加include第三方库的头文件路径
在模快的构造函数里加上：
	PublicIncludePaths.AddRange(
	        new string[] { 
	             Path.Combine(ThirdPartyPath, "xxx", "Include"),
	        }
	        );


	PrivateIncludePaths.AddRange(
	        new string[] {
	            Path.Combine(ThirdPartyPath, "Foxit", "Include"),
	        }
	        );
链接第三方库的Lib

接下来需要在编译工程时加入第三方静态库的链接，静态链接属于工程在编译期间做的事情，因此这块需要通过cs脚本完成，而dll动态链接库的加载是运行期的事，因此需要在cpp文件中执行。

我们新建一个叫LoadxxxLib的函数，并把它放在模块的构造函数结尾执行：

	public bool LoadxxxLib(TargetInfo Target)
	    {
	        bool isLibararySupported = false;
	        if ((Target.Platform == UnrealTargetPlatform.Win64) || (Target.Platform == UnrealTargetPlatform.Win32))
	        {
	            isLibararySupported = true;
	            string PlatformString = (Target.Platform == UnrealTargetPlatform.Win64) ? "Win64" : "Win32";
	            PublicAdditionalLibraries.Add(Path.Combine(LibraryPath, PlatformString + ".lib"));
	            PublicDelayLoadDLLs.Add(PlatformString + ".dll");
	            RuntimeDependencies.Add(new RuntimeDependency(LibraryPath + PlatformString + ".dll"));
	        }
	        return isLibararySupported;
	    }
这样就可以保证在编译期链接上我们的第三方lib。

链接动态DLL

这个工作需要在plugin的运行期完成，在插件的source文件下找到一个与插件名字同名的cpp文件打开。会看到一个StartupModule的函数，我们需要在这里得到dll文件的handle。

在StartupModule中添加下面的代码：

	void FXXXModule::StartupModule()
	{
	#if PLATFORM_64BITS
	    FString platform = TEXT("win64.dll");
	#else
	    FString platform = TEXT("win32.dll");
	#endif
	    FString path = IPluginManager::Get().FindPlugin("XXX")->GetBaseDir(); 
	    FString dllpath = path + "/ThirdParty/XXX/Lib/" + platform;
	    PdfDllHandle = FPlatformProcess::GetDllHandle(*dllpath);
	    if (!PdfDllHandle)
	    {
	        UE_LOG(LogTemp, Warning, TEXT("Failed to load PDF library."));
	    }
	}
这里我们用的是PluginManager找到的插件所在的路径，值得注意的是使用这个函数时需要在build.cs中加入

	PrivateDependencyModuleNames.AddRange(
	            new string[]
	            {
	                ...
	                "Projects",
	            }
	            );
否则工程会链接出错。

###UE4实现简单 AI###
UnrealEngine是一个非常强大的引擎，UE4基于DirectX11，拥有新的材料流水线、蓝图视觉化脚本、直观蓝图调试、内容浏览器、人物动画、Matinee影院级工具集、后期处理效果、热重载、模拟与沉浸式视角、实时游戏预览、AI人工智能、音频、中间件集成等一系列全新特性。

下面是用C++结合蓝图实现一个简单的AI功能。效果如下图:

![image](http://img.manew.com/data/attachment/forum/201701/12/171602f1w3xkj5m65q10og.gif)

首先创建一个C++的第三人称工程，UE4的工程有很多类型，我们可以根据自己的需要选择。 然后新建需要的C++文件，包括AIPatrol、AIPatrolController、AI巡逻的目标点AITargetPoint，类型分别为Character、Controller、TargetPoint。· 将上述三个文件扩展成蓝图，这样C++和蓝图可以结合起来，比较方便的实现一些功能。新建BehaviorTree、Blackboard。行为树用来实现AI的逻辑。

![image](http://img.manew.com/data/attachment/forum/201701/12/171603uj7hwcekgea7bgaa.png.thumb.jpg)

在AIPatrol上加BehaviorTree：

	UPROPERTY(EditAnywhere,Category=AI)//add behavior to character    class UBehaviorTree* BehaviorTree;

配置Character参数:

![image](http://img.manew.com/data/attachment/forum/201701/12/171607iso1z3doy5o4o4r2.png)

![image](http://img.manew.com/data/attachment/forum/201701/12/171607iso1z3doy5o4o4r2.png)

AIPatrolController.h文件：

	/*behavior tree component*/
	       UBehaviorTreeComponent* BehaviorComp;
	 
	        /*Our blackboard component*/
	        UBlackboardComponent* BlackboardComp;
	 
	        /*Blackboard keys*/
	        UPROPERTY(EditDefaultsOnly, Category = AI)
	               FName LocationToGoKey;
	 
	        UPROPERTY(EditDefaultsOnly, Category = AI)
	               FName PlayerKey;
	 
	        //store all TargetPoints
	        TArray<AActor*> PatrolPoints;
	 
	        virtual void Possess(APawn* Pawn) override;public:
	 AAIPatrolController();
	 
	 void SetPlayerCaught(APawn* Pawn);//caught:捕捉
	 
	 /*Inline getter functions*/
	 FORCEINLINE UBlackboardComponent* GetBlackboardComp() const { return BlackboardComp; }
	 FORCEINLINE TArray<AActor* > GetPatrolPoints() const { return PatrolPoints; }
.cpp文件：

    AAIPatrolController::AAIPatrolController()
    {
       /*initialize blackboard and behavior tree*/
       BehaviorComp = CreateDefaultSubobject<UBehaviorTreeComponent>(TEXT("BehaviorComp"));
       BlackboardComp = CreateDefaultSubobject<UBlackboardComponent>(TEXT("BlackboardComp"));
     
       /*Initialize blackboard keys*/
       PlayerKey = "Target";
       LocationToGoKey = "LocationToGo";
    }
     
    void AAIPatrolController::SetPlayerCaught(APawn* MyPawn)
    {
       if (BlackboardComp)
       {
      BlackboardComp->SetValueAsObject(PlayerKey, MyPawn);
       }
    }
     
    void AAIPatrolController::Possess(APawn* MyPawn)
    {
       Super::Possess(MyPawn);
     
       /*Get reference to the character*/
       AAIPatrol* AICharacter = Cast<AAIPatrol>(MyPawn);
       if (AICharacter)
       {
      if (AICharacter->BehaviorTree->BlackboardAsset)
      {
     BlackboardComp->InitializeBlackboard(*(AICharacter->BehaviorTree->BlackboardAsset));
      }
     
      /*Populate patrol point array*/
      UGameplayStatics::GetAllActorsOfClass(GetWorld(), AAIpPatrolPoint::StaticClass(), PatrolPoints);
     
      BehaviorComp->StartTree(*AICharacter->BehaviorTree);
     
       }
    }
玩家被抓住的方法：
AIPatrolController：

	void AAIPatrolController::SetPlayerCaught(APawn* MyPawn)//玩家被抓住
	{
	 if (BlackboardComp)
	 {
	        BlackboardComp->SetValueAsObject(PlayerKey, MyPawn);//给玩家添加key值
	 }
	}
AIPatrol:

	AAIPatrol::AAIPatrol()
	{
	       //Initialize senses
	       /**UPawnSensingComponent :
	       * SensingComponent encapsulates(封装) sensory(感知) (ie sight and hearing) settings and functionality for an Actor,
	       * allowing the actor to see/hear Pawns in the world. It does nothing on network clients.
	       */
	       PawnSensingComp = CreateDefaultSubobject<UPawnSensingComponent>(TEXT("PawnSensingComp"));
	       PawnSensingComp->SetPeripheralVisionAngle(90.0f);
	 
	}
	// Called when the game starts or when spawned
	void AAIPatrol::BeginPlay()
	{
	       Super::BeginPlay();
	 
	       if (PawnSensingComp)
	       {
	              /** OnSeePawn : Delegate to execute when we see a Pawn. */
	              PawnSensingComp->OnSeePawn.AddDynamic(this, &AAIPatrol::OnplayerCaught);
	       }
	}
	 
	void AAIPatrol::OnplayerCaught(APawn* Pawn)
	{
	       /*Get a reference to the palyer controller*/
	       AAIPatrolController* AIController = Cast<AAIPatrolController>(GetController());
	       if (AIController)
	       {
	              GEngine->AddOnScreenDebugMessage(-1, 5.0f, FColor::Red, TEXT("You having been caught!"));
	              AIController->SetPlayerCaught(Pawn);
	       }
	}
>创建BTTask的C++文件，命名为BTSelectPatrolPoint。注意，这时要在Source文件夹中找到ProjectName.Build.cs文件，添加GameplayTasks到PublicDependencyModuleName中。.build.cs文件是编译文件，位于游戏性模块的根目录中，它定义了UnrealBuildTool编译该模块时要使用的一些信息。
BTTask：

	EBTNodeResult::Type UBTSelectPatrolPoint::ExecuteTask(UBehaviorTreeComponent & OwnerComp, uint8 * NodeMemory)
	{
	 
	   //get AIPatrolController
	   AAIPatrolController* AICon = Cast<AAIPatrolController>(OwnerComp.GetAIOwner());
	 
	   // check the get is successful
	   if (AICon)
	   {
	          //Get Blackboard Component
	          UBlackboardComponent* BlackboardComp = AICon->GetBlackboardComp();
	 
	          // get patrolPoint
	          AAIpPatrolPoint* CurrentPoint = Cast<AAIpPatrolPoint>(BlackboardComp->GetValueAsObject("LocationToGo"));
	 
	          TArray<AActor*> AvailablePatrolPoints = AICon->GetPatrolPoints();
	          AAIpPatrolPoint* NextPatrolPoint = nullptr;
	 
	          // set NextPatrolPoint
	          if (AICon->CurrentPatrolPoint != AvailablePatrolPoints.Num() - 1)
	          {
	                 NextPatrolPoint = Cast<AAIpPatrolPoint>(AvailablePatrolPoints[++AICon->CurrentPatrolPoint]);
	          }
	          else
	          {
	                 NextPatrolPoint = Cast<AAIpPatrolPoint>(AvailablePatrolPoints[0]);
	                 AICon->CurrentPatrolPoint = 0;
	          }
	 
	          // set blackboard keyValue
	          BlackboardComp->SetValueAsObject("LocationToGo", NextPatrolPoint);
	          return EBTNodeResult::Succeeded;
	   }
	   return EBTNodeResult::Failed;
	 
	}
将BP_AIPatrol放入场景，摆放好BP_AIPatrolPoints，即AI 要走的路径，编辑BehaviorTree。
![image](http://img.manew.com/data/attachment/forum/201701/12/171608jjvct6tcecctjcaz.png)
BehaviorTree的思路是首先执行BTSelectPatrolPoint，如果没有找到目标，就move to key值为LocationToGo的点，如果找到了Target，就移动到Target处，然后在target处停留1秒。

 最后，添加自动寻路组件NavMeshBoundsVolume，这样AI在寻路过程中就会自动绕开障碍物。

###UE4中编写C++代码控制角色###
当你运行我们上次做完的项目，你可能会意识到我们移动的摄像机还是默认的那个摄像机，这个默认的摄像机可以自由飞翔。这一节，我们要使得开始的角色是我们的一个Avatar类的实例对象，并且使用键盘控制我们的角色。

一、创建游戏模式的步骤：

 1. 点击文件 --> 新建C++类。
![image](http://img.manew.com/data/attachment/forum/201701/20/102915omn1on1oj8j2anv8.png)
2. 选择Game Mode（游戏模式）。  
![image](http://img.manew.com/data/attachment/forum/201701/20/102925qkrgkffic1fvgrrh.png.thumb.jpg)
3. 将其命名为“MyGameMode1”。点击创建类。  
![image](http://img.manew.com/data/attachment/forum/201701/20/102952mfmpb8gbxobrpf88.png.thumb.jpg)
  
二、创建游戏模式的蓝图  
UE4会自动启动VS开发环境，然后我们来创建MyGameMode1蓝图：  

1. 如图所示操作  
![image](http://img.manew.com/data/attachment/forum/201701/20/103015hwppxoj8nan8a8vd.png.thumb.jpg)  
2. 填写蓝图名称，我这里是“BP_GameMode1”，然后点好。  
![image](http://img.manew.com/data/attachment/forum/201701/20/103048dignvgm0gu09qslh.png.thumb.jpg)
3. 从右侧的细节面板中的Default Pawn Class的下拉选项中选择上次我们创建好的角色蓝图BP_Avatar。  
![image](http://img.manew.com/data/attachment/forum/201701/20/103114j0zvylcewyceyy8k.png.thumb.jpg)  
什么是Default Pawn Class？Default Pawn Class就是被角色使用的那一类物体，也就是可以被玩家控制的Actor角色。
4. 点击工具栏的保存，然后退出。  

现在运行游戏的话，你可以看到我们使用的摄像头已经是BP_Avatar角色所包含的摄像头了。但是现在还是控制不了角色，因为我们还没设置控制器输入。

三、设置检测键盘输入

1. 点击工具栏的设置，然后点击项目设置。  
![image](http://img.manew.com/data/attachment/forum/201701/20/103148ytugu44hr40ghsfs.png.thumb.jpg)  
2. 接下来，点击左侧面板的输入，然后在Axis Mappings（按键映射）后面点击加号，再点击前面的小三角形展开。输入一个名为Forward（前进）的按键映射，然后下面选择W键。接着再添加一个名为Back（后退）的按键映射，然后下面选择D键。Left（左移）对应A键，Right（右移）对于D键。 
![image](http://img.manew.com/data/attachment/forum/201701/20/103159tqs91zo8iawqzfa8.png.thumb.jpg)  
3. 直接关闭该窗口以保存设置。

四、通过C++代码控制角色行走
1. 现在打开你的VS里面的Avatar.h构造器，添加五个成员函数的声明：
  
	//添加如下三个成员函数，用于角色控制：
	void SetupPlayerInputComponent(class UInputComponent* InputComponent) override; //覆写虚函数，当有输入的时候被调用以绑定功能
	void MoveForward(float amount);
	void MoveBack(float amount);
	void MoveLeft(float amount);
	void MoveRight(float amount);
并删除原有的这一行：virtual void SetupPlayerInputComponent(class UInputComponent* InputComponent) override;  
2.  然后在Avatar.cpp完成函数体定义：

	//游戏开始时被调用以绑定设备输入功能
	void AAvatar::SetupPlayerInputComponent(class UInputComponent* InputComponent)
	{
	    check(InputComponent); //检查空指针
	    InputComponent->BindAxis("Forward", this, &AAvatar::MoveForward);
	    InputComponent->BindAxis("Back", this, &AAvatar::MoveForward);
	    InputComponent->BindAxis("Left", this, &AAvatar::MoveRight);
	    InputComponent->BindAxis("Right", this, &AAvatar::MoveRight);
	}
上面的InputComponent::BindAxis(...)函数用于将按键信息于函数调用绑定。例如当玩家按下W键，引擎就会检测到有我们之前命名的"Forward"按键信息，然后自动去调用当前类的AAvatar::MoveForward(float amount)函数。其它三个按键也是如此运作。所以接下来定义好这四个函数就可以了，这里的参数amount是设备输入量经过与scale相乘后得出的结果：

	void AAvatar::MoveForward(float amount)
	{
	    // Controller控制器当前拥有该actor。例如如果当前角色死了，actor不存在了，此时Controller控制器没有拥有任何一个actor，那么如果我们还想去控制它，就会出错。
	    // 如果控制器没有拥有actor或者移动量是0，不能进入该函数。
	    if (Controller && amount)
	    {
	        // GetActorForwardVector()取得角色向前的向量
	        FVector fwd = GetActorForwardVector();
	        // 我们调用AddMovementInput来在‘fwd’向量的方向上移动角色‘amount’个单位
	        AddMovementInput(fwd, amount);
	    }
	}
	
	void AAvatar::MoveBack(float amount)
	{
	    if (Controller && amount)
	    {
	        // GetActorForwardVector()取得角色向前的向量，加上负号，该向量就向后，所以取得了角色向后的向量
	        FVector back = -GetActorForwardVector();
	        AddMovementInput(back, amount);
	    }
	}
	
	// 后面的函数类似前面，很容易懂
	void AAvatar::MoveLeft(float amount)
	{
	    if (Controller && amount)
	    {
	        FVector left = -GetActorRightVector();
	        AddMovementInput(left, amount);
	    }
	}
	
	void AAvatar::MoveRight(float amount)
	{
	    if (Controller && amount)
	    {
	        FVector right = GetActorRightVector();
	        AddMovementInput(right, amount);
	    }
	}
五、设置检测鼠标移动

接下来我们用第二步同样的操作打开项目设置并添加Yaw和Pitch按键信息，分别对应的是鼠标的X坐标和Y坐标。

注意Yaw的意思是绕竖轴旋转，Pitch的意思是绕横向轴旋转。见下图：  
![image](http://img.manew.com/data/attachment/forum/201701/20/103346h8n9nv58zvv4ne54.jpg.thumb.jpg)

通过C++代码控制角色镜头

在Avatar.h你需要添加两个函数声明：

	void Yaw(float amount);
	void Pitch(float amount);

然后在Avatar.cpp中实现它们：

	void AAvatar::Yaw(float amount)
	{
	if (Controller && amount)
	{
	    // AddControllerYawInput()函数用于改变控制器的Yaw变量，即增加纵向轴旋转量。
	    // GetWorld()函数取得世界指针UWorld*，通过世界指针调用GetDeltaSeconds()取得每帧耗费的时间。
	    // 之所以要乘以每帧耗费的时间，是为了使得每一【秒】都增加200.0f * amount的改变量。
	    // 如果不乘以每帧耗费的时间，那么每一【帧】都会增加200.0f * amount的改变量。（注意由于每秒渲染量不同，所以每秒的帧数不一定是固定的。）
	    // 通过帧数来控制变量，那么游戏看起来就不那么流畅。试想，机子性能好的时候游戏角色动作就迅速，机子性能差的时候游戏角色动作就慢，这对于玩家公平吗？
	    AddControllerYawInput(200.f * amount * GetWorld()->GetDeltaSeconds());
	}
	}
	  
	// 下面的函数与上面雷同，不再赘述
	void AAvatar::Pitch(float amount)
	{
	    if (Controller && amount)
	    {
	        AddControllerPitchInput(200.f * amount * GetWorld()->GetDeltaSeconds());
	    }
	}
在void AAvatar::SetupPlayerInputComponent(class UInputComponent* InputComponent)函数体的下面添加这两条语句将输入信息和函数绑定：

	InputComponent->BindAxis("Yaw", this, &AAvatar::Yaw);
	InputComponent->BindAxis("Pitch", this, &AAvatar::Pitch);
完整代码

	// Fill out your copyright notice in the Description page of Project Settings.

	#pragma once

	#include "GameFramework/Character.h"
	#include "Avatar.generated.h"
	  
	UCLASS()
	class MYFIRSTCODE_API AAvatar : public ACharacter
	{
	    GENERATED_BODY()
	  
	public:
	    // Sets default values for this character's properties
	    AAvatar();
	  
	    // Called when the game starts or when spawned
	    virtual void BeginPlay() override;
	      
	    // Called every frame
	    virtual void Tick( float DeltaSeconds ) override;
	  
	    // Called to bind functionality to input
	    //virtual void SetupPlayerInputComponent(class UInputComponent* PlayerInputComponent) override;
	  
	    //添加如下三个成员函数，用于角色控制：
	    void SetupPlayerInputComponent(class UInputComponent* InputComponent) override; //覆写虚函数，当有输入的时候被调用以绑定功能
	    void MoveForward(float amount);
	    void MoveBack(float amount);
	    void MoveLeft(float amount);
	    void MoveRight(float amount);
	  
	    void Yaw(float amount);
	    void Pitch(float amount);


	};

Avatar.cpp完整代码如下：

	// Fill out your copyright notice in the Description page of Project Settings.

	#include "MyFirstCode.h"
	#include "Avatar.h"


	// Sets default values
	AAvatar::AAvatar()
	{
	    // Set this character to call Tick() every frame.  You can turn this off to improve performance if you don't need it.
	    PrimaryActorTick.bCanEverTick = true;
	  
	}
	  
	// Called when the game starts or when spawned
	void AAvatar::BeginPlay()
	{
	    Super::BeginPlay();
	      
	}
	  
	// Called every frame
	void AAvatar::Tick( float DeltaTime )
	{
	    Super::Tick( DeltaTime );
	  
	}
	  
	// Called to bind functionality to input
	//void AAvatar::SetupPlayerInputComponent(UInputComponent* PlayerInputComponent)
	//{
	//  Super::SetupPlayerInputComponent(PlayerInputComponent);
	//
	//}
	  
	//游戏开始时被调用以绑定设备输入功能
	void AAvatar::SetupPlayerInputComponent(class UInputComponent* InputComponent)
	{
	    check(InputComponent); //检查空指针
	    InputComponent->BindAxis("Forward", this, &AAvatar::MoveForward);
	    InputComponent->BindAxis("Back", this, &AAvatar::MoveForward);
	    InputComponent->BindAxis("Left", this, &AAvatar::MoveRight);
	    InputComponent->BindAxis("Right", this, &AAvatar::MoveRight);
	  
	    InputComponent->BindAxis("Yaw", this, &AAvatar::Yaw);
	    InputComponent->BindAxis("Pitch", this, &AAvatar::Pitch);
	}
	void AAvatar::MoveForward(float amount)
	{
	    // Controller控制器当前拥有该actor。例如如果当前角色死了，actor不存在了，此时Controller控制器没有拥有任何一个actor，那么如果我们还想去控制它，就会出错。
	    // 如果控制器没有拥有actor或者移动量是0，不能进入该函数。
	    if (Controller && amount)
	    {
	        // GetActorForwardVector()取得角色向前的向量
	        FVector fwd = GetActorForwardVector();
	        // 我们调用AddMovementInput来在‘fwd’向量的方向上移动角色‘amount’个单位
	        AddMovementInput(fwd, amount);
	    }
	}
	  
	void AAvatar::MoveBack(float amount)
	{
	    if (Controller && amount)
	    {
	        // GetActorForwardVector()取得角色向前的向量，加上负号，该向量就向后，所以取得了角色向后的向量
	        FVector back = -GetActorForwardVector();
	        AddMovementInput(back, amount);
	    }
	}
	  
	// 后面的函数类似前面，很容易懂
	void AAvatar::MoveLeft(float amount)
	{
	    if (Controller && amount)
	    {
	        FVector left = -GetActorRightVector();
	        AddMovementInput(left, amount);
	    }
	}
	  
	void AAvatar::MoveRight(float amount)
	{
	    if (Controller && amount)
	    {
	        FVector right = GetActorRightVector();
	        AddMovementInput(right, amount);
	    }
	}
	void AAvatar::Yaw(float amount)
	{
	    if (Controller && amount)
	    {
	        // AddControllerYawInput()函数用于改变控制器的Yaw变量，即增加纵向轴旋转量。
	        // GetWorld()函数取得世界指针UWorld*，通过世界指针调用GetDeltaSeconds()取得每帧耗费的时间。
	        // 之所以要乘以每帧耗费的时间，是为了使得每一【秒】都增加200.0f * amount的改变量。
	        // 如果不乘以每帧耗费的时间，那么每一【帧】都会增加200.0f * amount的改变量。（注意由于每秒渲染量不同，所以每秒的帧数不一定是固定的。）
	        // 通过帧数来控制变量，那么游戏看起来就不那么流畅。试想，机子性能好的时候游戏角色动作就迅速，机子性能差的时候游戏角色动作就慢，这对于玩家公平吗？
	        AddControllerYawInput(200.f * amount * GetWorld()->GetDeltaSeconds());
	    }
	}
	  
	// 下面的函数与上面雷同，不再赘述
	void AAvatar::Pitch(float amount)
	{
	    if (Controller && amount)
	    {
	        AddControllerPitchInput(200.f * amount * GetWorld()->GetDeltaSeconds());
	    }
	}

六、最后工作

1. 删除多余角色

我们发现此时场景中有之前为了示例展示出来的多余的一个角色，我们选中该角色，按Delete键将其在场景中删除。

2. 删除多余碰撞体

1) 如下图所示打开BP_Avatar蓝图类编辑器  
![image](http://img.manew.com/data/attachment/forum/201701/20/103615k7xfjh3bmoimmhsh.png.thumb.jpg)

2) 因为我们已经有胶囊碰撞体了，所以不需要原来模型的碰撞体。在编辑器界面中进行如下操作，将碰撞预设值改为“NoCollision”即可。  
![image](http://img.manew.com/data/attachment/forum/201701/20/103625mbec3i1oqcz158uc.png.thumb.jpg)

经过本节的学习，现在我们的角色已经可以通过键盘前后左右移动和通过鼠标左右移动来绕yaw轴旋转身体了，而鼠标上下移动是不能绕pitch轴旋转身体的（这看起来也不自然），我们后面有其它用途。
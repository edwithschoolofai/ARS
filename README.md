# 확장 랜덤 탐색 (ARS)

ARS는 연속적인 제어 문제에 대한 선형 정책을 학습하는 랜덤 탐색 방법입니다. ARS는 다음 논문에 소개되어 있습니다. ["Simple random search provides a competitive approach to reinforcement learning."](https://arxiv.org/abs/1803.07055) 

## ARS를 위한 필수 구성 요소

ARS 실행을 위해서는 파이썬 3, OpenAI Gym 버전 0.9.3, mujoco-py 0.5.7, MuJoCo Pro 버전 1.31, 그리고 병렬 컴퓨팅을 위한 Ray library 가 필요합니다.

OpenAI Gym과 MuJoCo dependencies를 설치하려면 다음의 링크를 따라하면 됩니다:
https://github.com/openai/gym

Ray execute 설치를 위해서는 다음을 입력하면 됩니다:
``` 
pip install ray
```
Ray에 대해 더 알고 싶다면 다음의 링크를 읽어보세요: http://ray.readthedocs.io/en/latest/. 

## ARS 실행

먼저 아래의 명령어를 이용하여 Ray를 실행합니다:

```
ray start --head --redis-port=6379 --num-workers=18
```
이 명령어는 Ray를 이용한 병렬 컴퓨팅을 실행하기 위해 하나의 장치에서 여러 파이썬 프로세스를 실행시킵니다.
"num_workers=X" 에서 X는 몇 개의 CPU에 대해서 ARS 병렬 연산을 실행할 것인지를 의미합니다.
군집에서 ARS를 병렬 연산하기 위해서는 다음 링크를 참고하면 됩니다: http://ray.readthedocs.io/en/latest/using-ray-on-a-large-cluster.html.

아래의 명령어를 실행해 단일 스레드 선형 대수 연산을 실행하는 것을 권장합니다:
```
export MKL_NUM_THREADS=1
```

HalfCheetah-v1 문제의 정책을 학습하기 위해서는 아래의 명령어를 실행하면 됩니다:

```
python code/ars.py
```

ARS에 입력하는 모든 인수들은 선택사항이며, 다른 환경에 대해 학습하거나 다른 하이퍼파라미터를 사용하거나 다른 무작위 시드를 사용할 때마다 바꾸어 사용할 수 있습니다.
예를 들어, Humanoid-v1 문제의 정책을 학습할 때에는 다음의 명령어를 실행합니다:

```
python code/ars.py --env_name Humanoid-v1 --n_directions 230 --deltas_used 230 --step_size 0.02 --delta_std 0.0075 --n_workers 48 --shift 5
```

## 학습한 정책 렌더링하기

학습한 정책을 렌더링하기 위해서는 다음의 명령어를 실행합니다:

```
python code/run_policy.py trained_polices/env_name/policy_directory_path/policy_file_name.npz env_name --render
```

예를 들어, galloping gait를 포함한 Humanoid-v1를 렌더링하기 위해서는 다음의 명령어를 실행합니다:

```
python code/run_policy.py trained_policies/Humanoid-v1/policy_reward_11600/lin_policy_plus.npz Humanoid-v1 --render 
```

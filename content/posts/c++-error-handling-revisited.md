---
title: "C++ Error Handling Revisited - Raphael Meyer 보면서 정리"
date: 2020-11-14T00:00:00+09:00
draft: false
toc: false
categories:
  - studylog
tags:
  - c++
  - rust
  - code
  - exception
  - error
  - std::optional
  - std::variant
---

C++ 에서 에러 처리를 어떻게 할수 있는지에 대해 알아보는 [영상](https://youtu.be/Ybmomkc48wg)을 보면서 정리(?) 한 내용입니다.

영상에서는 haskell 예제를 보여주며 functional 컨셉을 설명하지만, (영어가 짧아 이해가 되지 않아) 단순히 변경되어가는 코드만 정리했습니다.
몇가지 코드(방법)이 더 있었는데 코드 자체가 이해가 안되서 정리하지 않았습니다.

<!--more-->

C/C++ 에서 일반적인 예외 처리는 다음과 같습니다.
```c++
bool MoistureSensor::read(Moisture &moisture) { /* ... */ }
bool Pump::pump(Volume amount) { /* ... */ }
bool WateringSystem::water(Volume& amount) {
  Moisture moisture;
  if (moisture_sensor.read(moisture)) {
    Temperature temperature;
    if (thermo_sensor.read(temperature)) {
      if (calculate_amount(moisture, temperatue, amount)) {
        if (pump.pump(amount)) {
          return true;
        }
      }
    }
  }
  return false;
}
```

이를 좀더 보기 편하게 정리하면 다음과 같이 쓸 수 있습니다.
```c++
bool MoistureSensor::read(Moisture& moisture) { /* ... */ }
bool Pump::pump(Volume amount) { /* ... */ }
bool WateringSystem::water(Volume& amount) {
  Moisture moisture;
  if (not moisture_sensor.read(moisture)) {
    return false;
  }
  Temperature temperature;
  if (not thermo_sensor.read(temperature)) {
    return false;
  }
  if (not calculate_amount(moisture, temperature, amount)) {
    return false;
  }
  if (not pump.pump(amount)) {
    return false;
  }
  return true;
}
```

C++17 에 포함된 std::optional 을 쓰면 다음과 같이 할 수 있습니다.
```c++
std::optional<Moisture> MoistureSensor::read() { /* ... */ }
std::optional<std::monostate> Pump::pump(Volume amount) { /* ... */ }
std::optional<Volume> WateringSystem::water() {
  auto const moisture = moisture_sensor.read();
  if (not moisture) {
    return std::nullopt;
  }
  auto const temperature = thermo_sensor.read();
  if (not temperature) {
    return std::nullopt;
  }
  auto const amount = calculate_amount(moisture.value(), temperature.value());
  if (not amount) {
    return std::nullopt;
  }
  if (not pump.pump(amount.value())) {
    return std::nullptr;
  }

  return amount;
}
```

std::variant 을 사용해서 에러 객체를 반환할수 있도록 개선할 수 있습니다.
```c++
std::variant<Moisture, Error> MoistureSencor::read() { /* ... */ }
std::variant<std::monostate, Error> Pump::pump(Volume amount) { /* ... */ }
std::variant<Volume, Error> WateringSystem::water() {
  auto const moisture = mosture_sensor.read();
  if (auto const error = std::get_if<Error>(&mositure); error) {
    return *error;
  }
  auto const temperature = thermo_sensor.read();
  if (auto const error = std::get_if<Error>(&temperature); error) {
    return *error;
  }
  auto const amount = calculate_amount(std::get<Moisture>(moisture), std::get<Temperature>(temperature));
  if (auto const error = std::get_if<Error>)(&amount); error) {
    return *error;
  }
  auto const pump_result = pump.pump(std::get<Volume>(amount));
  if (auto const error = std::get_if<Error>(&pump_result); error) {
    return *error;
  }
  return amount;
}
```

물론 C++ 이 제공하는 예외처리를 사용할 수 있습니다.
하지만, try catch 가 마치 goto 와 같이 느껴져서 좋아하지 않는 사람도 있습니다.
```c++
Moisture MoistureSensor::read() { /* ... */ }
void Pump::pump(Volume amount) { /* ... */ }
Volume WateringSystem::water() {
  try {
    auto const moisture = moisture_sensor.read();
    auto const temperature = thermo_sensor.read();
    auto const amount = calculate_amount(moisture, temperature);
    pump.pump(amount);
    return amount;
  }
  catch(SensorError const& e) {
    throw WateringError{e.what()};
  }
  catch(PumpError const& e) {
    throw WateringError{e.what()};
  }
}
```

RUST (rust-lang) 에서의 방법은 다음과 같습니다.
```rust
/*
enum Result<T, E> {
  Ok(T),
  Err(E),
}
*/
fn read_moisture() -> Result<Moisture, String> { /* ... */ }
fn pump(v: &Volume) -> Result<(), String> { /* ... */ }
fn water_plant() -> Result<Volume, String> {
  let moisture = match read_moisture() {
    Ok(m) => m,
    Err(e) => return Err(e),
  };
  let temperature = match read_temperature() {
    Ok(t) => t,
    Err(e) => return Err(e),
  };
  let amount = match calculate_amount(moisture, temperature) {
    Ok(v) => v,
    Err(e) => return Err(e),
  };
  return match pump(&amount) {
    Ok(_) => Ok(amount),
    Err(e) => Err(e),
  };
  /* 다음과 같이 줄여 쓸 수 있음. */
  /*
  let moisture = read_moisture()?;
  let temperature = read_temperature()?;
  let amount = calculate_amount(moisture, temperature)?;
  pump(&amount)?;
  return Ok(amount);
   */
}
```

C++ 에서 RUST 와 유사한 방법을 사용해보면 다음과 같이 할 수 있습니다.
```c++
template<typename OkType, typename ErrType>
class Result
{
  public:
  Result() = default;

  template<typename T, typename std::enable_if<std::is_convertible<T, std::variant<ErrType, OkType>>{}, int>::type = 0>
  Result(T &&value):_value{std::forward<T>(value)} {}

  ErrType const& err() const { return std::get<err_index>(_value); }
  OkType const& ok() const { return std::get<ok_index>(_value); }

  bool is_err() const { result _value.index() == err_index; }
  bool is_ok() const { result _value.index() == ok_index; }

  private:
  constexpr static std::size_t err_index = 0;
  constexpr static std::size_t ok_index = 1;
  std::variant<ErrType, OkType> _value;

  Result(std::variant<ErrType, OkType> value):_value{std::move(value)} {}

  friend auto make_ok<OkType, ErrType>(OkType value);
  friend auto make_err<OkType, ErrType>(ErrType value);
};

template<typename OkType, typename ErrType>
auto make_ok(OkType value) {
  using T = Result<OkType, ErrType>;
  return T{std::variant<ErrType, OkType>{
    std::in_place_index<T::ok_index>,
    std::move(value)
  }};
}

template<typename OkType, typename ErrType>
auto make_err(ErrType value) {
  using T = Result<OkType, ErrType>;
  return T{std::variant<ErrType, OkType>{
    std::in_place_index<T::err_index>,
    std::move(value)
  }};
}

Result<Moisture, Error> MoistureSensor::read() { /* ... */ }
Result<std::monostate, Error> Pump::pump(Volume amount) { /* ... */ }
Result<Volume, Error> WateringSystem::water() {
  auto const moisture = moisture_sensor.read();
  if (moisture.is_error()) {
    return moisture.err();
  }
  auto const temperature = thermo_sensor.read();
  if (temperature.is_err()) {
    return temperature.err();
  }
  auto const amount = calculate_amount(moisture.ok(), temperature.ok());
  if (amount.is_err()) {
    return amount.err();
  }
  auto const pump_result = pump.pump(amount.ok());
  if (pump_result.is_err()) {
    return pump_result.err();
  }
  return amount;
}

int main() {
  auto const amount = WateringSystem{}.water();
  if (amount.is_ok()) {
    std::cout<<"Water "<<amount.ok().ml<<" ml\n";
  } else {
    std::cout<<amount.err().what<<"\n";
  }
}
```

Use **value types** to write code that is _expressive_ and clearly _communicates_ its _intent_.
라고 합니다.